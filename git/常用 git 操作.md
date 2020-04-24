- 重新修改远程仓库地址  

```git
git remote set-url <自定义名称> <url>
```

- 为本地库添加新的远程仓库关联

```git

// 1. 添加关联
git remote add <自定义名称> <url>

// 2. 向新远程库 push
git push -u <自定义名称> master
```

- 删除 remote 关联

```git
git remote rm <关联名称> 
```

- 添加 `.gitignore` 后如何重新提交

```git
// 1. 清空缓存
git rm -r --cached .

// 2. 重新添加追踪
git add .

// 3. 重新 commit
git commit -m "update .gitignore"

// 4. 重新 push
git push
```


- 查看 git 仓库大小（包含历史记录等）

```
du -hs .git
```


- 合并 commit 减小 git 仓库大小

```
# 获取最远 commit 的 id
git log

# reset 合并 commit
git reset 5039e52ca932349152c519749de265ec36ef6001

git add .

git commit -m ""

# 强制推送到远程端
git push origin HEAD:master --force
```


