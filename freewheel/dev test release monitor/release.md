## release流程
1. 参考：[Log In - FreeWheel WiKi](https://wiki.freewheel.tv/display/~dqyang/Stats+Engine+Release+Process) 
2. 准备工作

```
	1. 密钥id_rsa.lg传输到/root/.ssh目录下
	2. 操作前执行build.sh中的命令，对自己的id_rsa来进行认证
```
3. 正常release

```
	1. 创建release-wiki-page 【本次release的tickets，改动的conf】
	2. 通过stg的跳板机，登录ssh root@build-preprod.stickyadstv.com
	3. 进入/root/se-GIT/lite-stats目录
	4. git branch -vv 展示当前所有本地分支
	5. git checkout develop 切换到develop分支
	6. 执行build.sh中的两行命令，之后就可以自如地正常进行git操作（不需要再用到GIT_SSH=xxx.sh前缀）
	7. git pull 拉取最新代码，update
	7. git log --decorate --oneline --graph --color 展示代码改动，check
	8. git checkout -b release/2.9.X 创建并切换到本地的新release分支
	9. git branch -vv 展示本地所有分支，check
	10. git log --decorate --oneline --graph --color 展示代码改动，check
	11. git push origin release/2.9.X 将当前本地分支push到远程仓库
	12. git branch -vv 展示本地所有分支，check
	13. git branch -r 展示远程所有分支，check
	14. git tag 展示本地tag
	15. git tag 2.9.X.0 为当前release分支创建新的tag
	16. git log --decorate --oneline --graph --color 展示代码改动，check
	17. git push origin 2.9.X.0 将本地tag push到远程仓库
	18. git log --decorate --oneline --graph --color 展示代码改动，check
	19. ant clean, ant 代码打
	20. mv liestats-2.9.X.0.tar.gz /home/release/ 移动代码jar包到目标文件夹
	21. 更新wiki，新建preprod的ticket，@ssp data team，s@Ops team
	22. 在Ops进行deploy之后，观察staging上mapstats和redstats的running log情况
	23. 在preprod的ticket下comment运行结果
	24. 新建prod的ticket，@ssp data team，@Ops team
	25. phase1和phase2机器上进行running log的观察，跑Non-Regression测试，在ticket下comment运行情况
	26. git tag 展示本地tag
	27. git tag 2.9.X.0p 从本次release分支上创建新的tag
	28. GIT_SSH=/root/lgs.sh git push origin 2.9.X.0p 将新的tag push到远程仓库
```



