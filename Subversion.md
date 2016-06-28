## Subversionメモ

### 無視リスト
以下の内容で.svnignoreを作成

```
*.o *.lo *.la #*# .*.rej *.rej .*~ *~ .#* .DS_Store thumbs.db Thumbs.db *.bak *.class *.exe *.dll *.mine *.obj *.ncb *.lib *.log *.idb *.pdb *.ilk *.msi* .res *.pch *.suo *.exp *.*~ *.~* ~*.* cvs CVS .CVS .cvs release Release debug Debug ignore Ignore bin Bin obj Obj *.csproj.user *.user *.generated.cs
```

指定したいsvnリポジトリのトップディレクトリで以下のコマンドを実行。

```bash
$ svn propset svn:ignore -R -F .svnignore .
```
