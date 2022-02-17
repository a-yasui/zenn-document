---
title: "開発環境の開発"
---

三通りの開発環境を Laravel では選ぶことができます。

- Plane

	Plane なんてパッケージは有りません。つまり、ローカルマシンにHomeBrew などを使って、PHP/MySQL をインストールする形です。構築等がとても大変な古典的なやり方です。
	個人的に非推奨です。

- Homestead

	Vagrant+Virtualbox/Parallels を使い、Ubuntu環境で動かす形。
	複数のプロジェクトがあるときに、それらの分を Homestead.yaml に追記すれば動くようになるので、プロジェクトが多い時にマシンリソースが Docker に比べてましです。

	https://github.com/laravel/homestead

- Sail

	Docker Compose を含んだPHPのパッケージを composer で取り込んで起動させる形。
	プロジェクト単位にインストールをするので気軽に始められます。ただし、他のプロジェクトでも使用して立ち上げている時に Port が重複したり、リソース食いになってマシンが重くなったりします。

	https://github.com/laravel/sail
