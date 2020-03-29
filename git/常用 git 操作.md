1. 重新修改远程仓库地址  

```git
git remote set-url <自定义名称> <url>
```

2. 为本地库添加新的远程仓库关联

```git

// 1. 添加关联
git add remote <自定义名称> <url>

// 2. 向新远程库 push
git push -u <自定义名称> master
```

3. 添加 `.gitignore` 后如何重新提交

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