# Build & Release

[TOC]

## Makefile

make 命令执行，例如：`make build-debian`

```shell
SERVICE_NAME=se-avatar-engine
VERSION=$(shell git describe --tag --always HEAD)

clear:
   rm -rf ./bin
   @echo $(REVISION)

build:
   go build -o ${SERVICE_NAME} ./main.go

run:
   go run ./main.go -c config/config.yml

build-debian: clear
   CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o ./bin/${SERVICE_NAME} ./main.go

tar-debian: build-debian
   tar c bin | gzip - > ${SERVICE_NAME}-${VERSION}.tar.gz
```

## Release steps

https://wiki.freewheel.tv/pages/viewpage.action?pageId=220533848

```
1. git operations
build server: root@build-preprod.stickyadstv.com (through staging jump server)
dir: /root/se-GIT/go
show all branches: git branch -vv
checkout remote master branch: git checkout master
pull latest code: git pull
show code changes: git log --decorate --oneline --graph --color
create and checkout new local release branch: git checkout -b release-avatar/X.X.X
show all branches: git branch -vv
show code changes: git log --decorate --oneline --graph --color
push local release branch to remote: git push origin release-avatar/X.X.X
show all tags: git tag
create new tag: git tag avatarX.X.X.0
show code changes: git log --decorate --oneline --graph --color
push local tag to remote: git push origin avatarX.X.X.0
show code changes: git log --decorate --oneline --graph --color


2. build
dir: /root/se-GIT/go/se_avatar_engine
make build-debian
make tar-debian
mv se-avatar-engine-X.X.X.X.tar.gz /home/releases/
```

## Test DB

mysql --user stickyadstv_rw --host 10.13.17.14 -P3307 -pstickyadstv_rw



## Cut Over

NY5 DB: mysql --user stickyadstv_rw --host 10.13.17.2 -P3306 -pstickyadstv_rw

[用daily-report连]

# HA

https://github.freewheel.tv/ssp/sysadmin/pull/605/files

Ops自动化部署























