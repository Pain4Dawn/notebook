## tag
```shell
#列出标签
git tag
# 创建标签
git tag -a v1.4 -m ""
# 推送标签
git push origin v1.4
# 删除本地标签
git tag -d v1.4
# 删除远程标签
git push origin :refs/tags/v1.4
## 回退版本
git reset --hard 
## 删除没有add的文件
git checkout -f
## 退回上一次提交，
git reset --soft HEAD~1
## 撤销上次操作,提交有记录
git revert HEAD
```

## config
```shell
# 打印所有
git config -l
# 设置
git config user.name "user"
# 删除
git config --unset user.name
```

[git 标签操作](https://blog.csdn.net/happycxz/article/details/78893690)
