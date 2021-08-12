[TOC]

## 合并多个commit

```shell
git log
git rebase -i HEAD~n (n是希望合并的最近的记录的条数)
更改弹出的git commit记录，只对第一条使用pick，其余的将pick改为s
更改commit message
git status
git push -f（强制提交，更改remote commit）
```

https://blog.csdn.net/qq_21744873/article/details/82629343



## 暂时隐藏当前修改并归档，供下次使用

```shell
git stash
git stash apply
```



## 以版本树的形式展示git log

```
git log --decorate --oneline --graph --color
```



## git rebase合并最新的master代码

The complete form:

```
git fetch
git rebase origin/master
```

Or the shorter form:

```
git pull --rebase
```

使用git base来合并feature开发分支和master分支的时候，commit的记录就不会和其他feature分支错乱在一起



## Failed to connect to github.com port 443: Operation timed out

```shell
lugao@lugao-mac ~/luluwork git push origin master
fatal: unable to access 'https://github.com/gaolu9595/luluwork.git/': Failed to connect to github.com port 443: Operation timed out
lugao@lugao-mac ~/luluwork git remote rm origin
lugao@lugao-mac ~/luluwork git remote add origin https://github.com/gaolu9595/luluwork.git
lugao@lugao-mac ~/luluwork git push origin master
Enumerating objects: 67, done.
Counting objects: 100% (67/67), done.
Delta compression using up to 12 threads
Compressing objects: 100% (47/47), done.
Writing objects: 100% (50/50), 54.42 KiB | 4.95 MiB/s, done.
Total 50 (delta 13), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (13/13), completed with 12 local objects.
To https://github.com/gaolu9595/luluwork.git
   103c58b..c579776  master -> master
```

