# Ubuntu 22.04 のシステムフォントを変更する

日本語フォントが基本 DroidSansFallback になっているために、特定の漢字が中華フォント化するので、日本語を優先的に使うようにする。

フォントシステム自体は昔（いつ？）から変更はほぼないみたい。変更あったらちょっとつらい。

# 対処

今回は IPA フォントを使い、それは予め apt でインストールしているとする。

1. */etc/fonts/local.conf* に下記の xml を入れる。

ちょっとずらしてて、 *sans* と *serif* は明朝体、 *sans-serif* はゴシック体という状態にしている。

font.dtd はおそらく [chromium プロジェクトにある fontconfig -- chromium.googlesource.com](https://chromium.googlesource.com/external/fontconfig/+/ba15d41bdc0f6e949089d71208f8afdc99e1d19b/fonts.dtd) を参考にした。

```xml
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>
    <match target="pattern">
        <test qual="all" name="family">
            <string>sans</string>
        </test>
        <edit name="family" mode="assign">
            <string>IPAPMincho</string>
        </edit>
    </match>
    <match target="pattern">
        <test qual="all" name="family">
            <string>serif</string>
        </test>
        <edit name="family" mode="assign">
            <string>IPAPMincho</string>
        </edit>
    </match>
    <match target="pattern">
        <test qual="any" name="family">
            <string>sans-serif</string>
        </test>
        <edit name="family" mode="assign">
            <string>JPAGothic</string>
        </edit>
    </match>
    <match target="pattern">
        <test qual="any" name="family">
            <string>mono</string>
        </test>
        <edit name="family" mode="assign">
            <string>JPAGothic</string>
        </edit>
    </match>
</fontconfig>
```

2. `fc-cache -f` で再構築させる

# 確認方法

```shell:変更前
/etc/fonts# fc-match :lang=ja
DroidSansFallbackFull.ttf: "Droid Sans Fallback" "Regular"
```

```shell:変更後
/etc/fonts# fc-match :lang=ja                                                                                                               
fonts-japanese-gothic.ttf: "IPAGothic" "Regular"
```

# 参考

- https://atmarkit.itmedia.co.jp/ait/articles/1812/13/news030.html
- https://blog.nhiroki.net/2020/12/12/prefer-japanese-font-fontconfig
- https://qiita.com/keith_campbell/items/3c3e2752dc44a58709b0
- https://teratail.com/questions/108040


