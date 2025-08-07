---
title: "Ubuntu 24.04 に Nvidia Driver を再インストールした"
emoji: "🐈"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [ 'ubuntu','linux','nvidia' ]
published: true
---

Nvidia 1060 が刺さってるマシンがありまして、久しぶりに `sudo apt upgrade -y` をしたら `nvidia_smi` が "NVIDIA-SMI has
failed because it couldn’t communicate with the NVIDIA driver. Make sure that the latest NVIDIA driver is installed and
running." と返すようになってハマったメモ。

## 環境

```shell
> neofetch
            .-/+oossssoo+/-.               yasui@frigg
        `:+ssssssssssssssssss+:`           -----------
      -+ssssssssssssssssssyyssss+-         OS: Ubuntu 24.04.3 LTS x86_64
    .ossssssssssssssssssdMMMNysssso.       Host: HP Pavilion Power Desktop 580-0xx 1.01
   /ssssssssssshdmmNNmmyNMMMMhssssss/      Kernel: 6.11.0-29-generic
  +ssssssssshmydMMMMMMMNddddyssssssss+     Uptime: 8 hours, 51 mins
 /sssssssshNMMMyhhyyyyhmNMMMNhssssssss/    Packages: 1825 (dpkg), 15 (snap)
.ssssssssdMMMNhsssssssssshNMMMdssssssss.   Shell: zsh 5.9
+sssshhhyNMMNyssssssssssssyNMMMysssssss+   Resolution: 2560x1080
ossyNMMMNyMMhsssssssssssssshmmmhssssssso   Terminal: /dev/pts/0
ossyNMMMNyMMhsssssssssssssshmmmhssssssso   CPU: Intel i7-7700 (8) @ 4.200GHz
+sssshhhyNMMNyssssssssssssyNMMMysssssss+   GPU: NVIDIA GeForce GTX 1060 6GB
.ssssssssdMMMNhsssssssssshNMMMdssssssss.   Memory: 721MiB / 15923MiB
 /sssssssshNMMMyhhyyyyhdNMMMNhssssssss/
  +sssssssssdmydMMMMMMMMddddyssssssss+
   /ssssssssssshdmNNNNmyNMMMMhssssss/
    .ossssssssssssssssssdMMMNysssso.
      -+sssssssssssssssssyyyssss+-
        `:+ssssssssssssssssss+:`
            .-/+oossssoo+/-.
```

## 現象と原因と対処

アップグレード前までは GPU の状態が見られたが、見れなくなった。

```shell
> nvidia_smi
NVIDIA-SMI has failed because it couldn’t communicate with the NVIDIA driver. Make sure that the latest NVIDIA driver is installed and running.
```

原因は Linux Kernel 6.14 に上げてしまった事。

- see: https://forums.developer.nvidia.com/t/fc42-kernel-6-14-1-570-133-07-fails-to-compile-modules/330104
- see: https://askubuntu.com/questions/1553090/nvidia-driver-470-not-compiling-properly-on-ubuntu-24-04-after-update

対処としては色々やり過ぎたためよくわからない事になっている。とりあえず Linux Kernel を 6.11 にダウングレード & Secure Boot で引っかかってたので MOK を登録した。

## 参考

- [https://zenn.dev/hagifly/articles/16ae709805a48c](CUDA をインストールせずに NVIDIA ドライバーをインストールする方法 -- zenn.dev)
- [https://takayama-rado.com/articles/update_nvidia_0.html](Ubuntuの更新ついでにNVIDIAのドライバを更新しようとしたらハマった話 -- takayama-rado.com)

# AI にまとめさせた物

以下は gpt-oss:20b にまとめさせた物。色々やり過ぎたため、やったほうも上手くmemoを取れてなかった。出力に時間かかったね…

## NVIDIA 555 ドライバ導入 ― 最終作業報告書（正式版）

| 項目              | 内容                                                |
|-----------------|---------------------------------------------------|
| **対象マシン**       | Ubuntu 24.04.3 LTS / Kernel 6.11.0‑29‑generic（GA） |
| **GPU**         | GeForce GTX 1060 6 GB（Pascal）                     |
| **導入ドライバ**      | nvidia‑driver‑555 (555.42.06) + CUDA 12.5         |
| **Secure Boot** | 有効（MOK Enrollment 既に完了）                           |

---

### 1. トラブルと対処フロー（時系列）

| Step | 操作                                   | 症状／原因                                                                       | 対処                                                                                                                              |
|------|--------------------------------------|-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| 1    | `sudo apt install nvidia-driver-555` | `libnvidia-egl-gbm1` と `libnvidia-gl-555` が同一 JSON ファイルを提供し、**dpkg 衝突** が発生 | `sudo apt purge libnvidia-egl-gbm1` で競合パッケージを削除                                                                                 |
| 2    | インストール再試行                            | DKMS が **kernel 6.14.0‑27** でビルド失敗（`bad exit status: 2`）                    | 6.14 系は NVIDIA 555 未対応であることを確認                                                                                                  |
| 3    | GRUB で **6.11** カーネル起動               | 6.14 でのビルドエラーを回避                                                            | GA カーネルで起動確認（`uname -r`）                                                                                                        |
| 4    | **6.14 カーネル** を削除                    | `sudo apt remove --purge 'linux-image-6.14.*' 'linux-headers-6.14.*'`       | DKMS は 6.11 用モジュールのみを保持                                                                                                         |
| 5    | 依存関係修復                               | `sudo apt --fix-broken install && sudo apt autoremove --purge`              | `nvidia-dkms-555` が正常に完了                                                                                                        |
| 6    | `nvidia-smi` 実行                      | **Secure Boot** でドライバがロードされず、`nvidia-smi` がエラー                              | MOK 公開鍵（`/var/lib/shim-signed/mok/MOK.der`）を登録（`sudo mokutil --import /var/lib/shim-signed/mok/MOK.der`）し、再起動→「Enroll MOK」→パス入力 |
| 7    | 動作確認                                 | `nvidia-smi` 正常出力                                                           | ドライバ・CUDA・Xorg の連携が確認できた                                                                                                        |

---

### 2. 現状構成（コマンド出力抜粋）

```bash
$ uname -r
6.11.0-29-generic

$ dkms status
nvidia/555.42.06, 6.11.0-29-generic, x86_64: installed

$ nvidia-smi   # 2025-08-07 01:39 JST
NVIDIA-SMI 555.42.06   •   Driver Version: 555.42.06   •   CUDA Version: 12.5
┌───────────────────────────────────────────────────────────────────────┐
│ GPU  Name        Pwr  Temp   Perf  Memory-Usage  Bus-Id          Disp.A │
│  0   GTX 1060    6 W   49 °C  P8         59 MiB  00000000:01:00.0  On │
└───────────────────────────────────────────────────────────────────────┘
```

---

### 3. 今後の留意点

1. **カーネル更新管理**
    - GA 系（`linux-generic`）は自動更新のみとし、Edge/HWE 系を導入する場合は、対応ドライバ（R560 以降）公開を確認した上で実施する。
2. **Secure Boot 運用**
    - DKMS で新バージョンをビルドする度に MOK 署名が必須。  
      `mokutil --import <derファイル>` → 再起動→「Enroll MOK」→パス入力を実施。
3. **不要パッケージ定期掃除**
    - `sudo apt autoremove --purge` で不要残骸を除去。
    - `dkms status` で不要カーネル向けビルドが残っていないかを定期的に確認。
4. **トラブル時の確認順序**
    1. カーネルバージョンとドライバ対応表の照合
    2. Secure Boot 設定の確認
    3. nouveau 競合の有無
    4. DKMS ログ（`/var/lib/dkms/.../make.log`）の確認

---

以上により、GeForce GTX 1060 Ti 上で NVIDIA 555 ドライバが正常に稼働していることを確認しました。今後も同様の手順を参考に運用を継続し、問題が再発した際は本報告書を参照してください。