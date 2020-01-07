```shell
# 地址默认别名
origin

# 生成其他不同的Key
ssh-keygen -t rsa -f ~/.ssh/id_rsa_icloud.git -C "lilei13033016020@icloud.com"

# 修改 全局的gitconfig，适用于当前登陆用户
git config --global user.email "xiaoming1@xxx.com"
git config --global user.email "xiaoming1@xxx.com
  
# 修改 项目中的gitconfig，适用于当前项目。
git config user.name "Schroedinger Cat iCloud "
git config user.email "lilei13033016020@icloud.com"
 
# 查看别名 
git remote

# 查看配置
git config --list

# 初始化仓库
git init

# 查看文件状态
git status

# 将文件放置暂存区
git add *  或 git add -A

#将文件提交到git仓库
git commit -m'第一次提交'





```

```shell
# 创建分支
git branch 分支名称

# 查看所有分支
git branch

# 切换分支
git checkout 分支名称

# 合并分支
git merge 合并分支名称

# 删除分支
git branch -d 删除分支名称

```

```shell
# 克隆代码
git clone 仓库地址

# 提交到共享仓库
git push 共享仓库地址 分支名称

# 共享仓库更新代码
git pull 共享仓库的地址 分支名称

```

```shell
# 版本回退
# 查看提交历史
git log

# 查看所有操作历史 
git reflog

# 暂存区会退
git reset HEAD

#  工作区会发生变化 , 版本记录也会发生变化
git reset --hard  SHA值  

#  版本记录会发生变化, 工作区没有发生变化, 回退到暂存区。
git reset --soft SHA值

#版本记录会发生变化, 工作区没有发生变化, 回退到已修改状态
git reset --mixed SHA值
					 
# 版本差异
 git diff 工作区与暂存区做对比
 git diff sha1 sha2  两个版本之间进行对比。

# 创建忽略文件 (忽略img下的图片: img/*,忽略.html文件: *.html)
vi .gitignore
```

