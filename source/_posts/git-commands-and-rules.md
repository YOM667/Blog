---
title: git命令和提交规范
date: 2023-07-17 21:08:05
tags: 
- 命令 
- 规范
---
## 命令
> git init "初始化一个 Git 仓库"
> git add . "添加所有文件"
> git commit -m "message" "提交 附带 提交信息"
> git pull "拉取远程仓库代码并合并"
> git push origin main "推送 到 远程仓库 的 main 分支" 
> git push origin maseter "推送 到 远程仓库 的 maseter 分支" 
> git remote set-url url "设置仓库远程地址"
> git remote -v "列出 remotes "
> git config --global http.proxy ip:port "设置http代理"
> git config --global https.proxy ip:port "设置https代理"
> git config --global --unset http.proxy "取消http代理"
> git config --global --unset https.proxy "取消http代理"
> git reset --soft HEAD~1 "撤销最后一次的 commit "
> git commit --amend "重新设置提交信息"
> git branch "创建分支"
> git merge "合并分支"
> git branch -d (branchname) "删除分支"
> git log "查看提交历史"
> git blame (filename) "查看文件修改记录"
## 提交规范
> feat 或 feature "新的功能"
> 示例: feat(XXX): add new feature about…
> fix  "修复错误"
> 示例: fix(XXX): fix bugs about…
> docs "修改或添加文档"
> 示例: docs(XXX): add new docs about…

