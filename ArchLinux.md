# Arch Linuxメモ
## Install from AUR
### 概要
公式以外のサードパーティーリポジトリ。

ソース配布しており、makepkgコマンドを使ってビルドする。

1. AURからtarballをDL.
2. 解凍
```
$ tar -xvf <package>.tar.gz
```
3. ビルド・インストール
```
$ makepkg -sri
```
