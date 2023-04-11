```
# 本地创建分支推送到远端
git push origin feature-branch:feature-branch
#  
```

## git 删除文件及还原
git删除文件需要在git版本上删除记录，删除完后需要还原的话需要做reset操作，具体指令如下
```
删除文件及文件夹
git rm -d file
还原文件，从commit到add
git reset HEAD file
从 add到原先
get checkout file
```
## 统计代码行数
```
git log --format='%aN' | sort -u | while read name; do echo -en "$name\t"; git log --author="$name" --pretty=tformat: --since ==2023–01-01 --until=2023-01-31 --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }' -; done | grep  "zhuzhe5\|cheyuanhang\|dupengfei\|liuguishuo"
```