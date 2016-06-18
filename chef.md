# chefメモ

## install (by gem)
```
$ gem install chef
```

## cookbook
### create
```
$ knife cookbook create <cookbook-name>
```
-oオプションでディレクトリ内に作れる：
```
knife cookbook create <cookbook-name> -o <output-directory>
```
### delete?
```
$ knife cookbook delete <cookbook-name> -z
```
* -zはローカル

## reference
* [Chefの基本](http://qiita.com/kidach1/items/9d569b8673e70ef93f0e)
