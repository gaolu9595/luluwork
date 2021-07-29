



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
