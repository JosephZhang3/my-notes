## 修改用户名邮箱
安装git或者换电脑环境后要做的第一件事

`git config user.name "newName"`

`git config user.email "newEmail"`

## Git凭证
在不使用ssh的情况下，如何避免每次push都重复输入用户名密码？
使用凭证助手:
`git config credential.helper store`

## 取消追踪一个已被追踪的文件
`git rm --cached`

## 使用vim而不是nano编辑文本
`git config --global core.editor vim`

或者在环境变量中设置GIT_EDITOR:

`export GIT_EDITOR=vim`

让所有的程序都使用vim编辑文本

`export VISUAL=vim`

`export EDITOR="$VISUAL"`

## 修改最近一条commit的用户名用户名邮箱信息
`git commit --amend --author="jianghaozhang <jianghaozhang@qq.com>" --no-edit`

## 合并最近的3条commit
`git rebase -i HEAD~3`

## 合并指定的commit
可能是多个版本之前的，[commitid] 本身不参与合并

`git rebase -i [commitid]`

## 完成或放弃
`git rebase --continue`

`git rebase --abort`

## 强推remote
做了rebase操作后，千万不要按照提示pull代码了，否则会覆盖本地导致前功尽弃。直接强推到远端

`git push -f`