# Subversionメモ

## 表示
### 指定したリビジョンの変更点を見る
r42の変更点をみる
```bash
$ svn log --verbose -r 42
```

### diffに色をつける
1. colordiffをインストール
```bash
$ sudo apt-get install colordiff
```

2. svnのdiffエディタに指定する
```config
# ~/.subversion/config
diff-cmd = colordiff
```

## 過去に戻る
### フォルダ単位で（コミット前の）変更取り消し
```bash
$ svn revert -R .
```

### 過去のリビジョンに戻る
```bash
$ svn update -r 9
```
これでリビジョン9に戻る。

or

```bash
$ svn merge -c -666 .
```
リビジョン666をリバートする。

## 無視リスト
注意：無視リストを使うときは'svn add --force .'を使う（明示的にファイルを追加した場合、svn:ignoreプロパティは無視される）。

### 無視リストをエディターで編集
```bash
$ EDITOR=vi svn propedit svn:ignore <PATH>
```
<PATH>には無視リストを設定するディレクトリを指定。

### 無視リスト（コマンド）
```bash
$ svn propset svn:ignore -R *.class .
```

-R でサブディレクトリも見る。

### 無視リスト (ファイル)
以下の内容で.svnignoreを作成

```
*.o
*.lo
*.la
#*#
.*.rej
*.rej
.*~
*~
.#*
.DS_Store
thumbs.db
Thumbs.db
*.bak
*.class
*.mine
*.obj
*.ncb
*.log
*.tlog
*.idb
*.pdb
*.ilk
*.msi
*.res
*.pch
*.suo
*.exp
*.sdf
*.opensdf
*.*~
*.~*
~*.*
release
Release
debug
Debug
Debug_x64
ignore
Ignore
bin
Bin
obj
Obj
*.csproj.user
*.vcxproj.user
*.user
*.generated.cs 
```

指定したいsvnリポジトリのトップディレクトリで以下のコマンドを実行。

```bash
$ svn propset svn:ignore -R -F .svnignore .
```

#### 注意点
svn addで追加するとき、*でフォルダを指定すると、ディレクトリに無視リストが反映されない。一つ上に行ってトップディレクトリからsvn add Directoryをするとよい。

### 無視リストを表示
プロパティを全表示。無視リスト以外も表示される。
```bash
$ svn proplist -v [PATH]
```

### 無視されたファイルを確認
```bash
$ svn status --no-ignore
```

### 自分のみを追加
```bash
$ svn add --depth=empty
```


## 参考
* [Getting svn to ignore files and directories](http://superchlorine.com/2013/08/getting-svn-to-ignore-files-and-directories/)
