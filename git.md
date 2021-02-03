git init

git add .

git status

git commit -m ""



git remote add origin git@github.com:shursulei/linux-all-tools.git



git push -u origin master



 git pull --rebase origin master



<https://www.cnblogs.com/smfx1314/p/8426115.html>



git push origin master





```
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:shursulei/pythonBigdata.git
git push -u origin main
```



```
git命令:
1、git status  查看是否是一个仓库
2、git init	   初始化仓库
3、git add a.md
4、git commit
5、git branch	查看分支
6、git branch a 创建分支 a
7、git checkout a 切换到a分支
8、git checkout -b a 新建并切换到分支上
9、git merge a 	先切换到master上面，要考虑到合并出现冲突的问题
10、git branch -d a删除a分支
11、git branch -D 强制删除
12、git tag 查看分支
13、git tag v1.0 创建分支
14、git checkout v1.0  切换分支


15、 git push origin master 本地代码推送到远端
16、git pull origin master   远端代码拉到本地

17、git clone git@github.com:shursulei/wms.git
18、git remote add origin git@github.com:shursulei/wms.git


ssh连接github
cmd下ssh -T git@github.com

19、git log				获取日志信息

20、设置编辑   git config --global core.editor "vim"
21、git config --global color.ui true
21、 			git config --global core.quotepath false # 设置显示中文文件名



分支管理流程

https://www.jianshu.com/p/58e2c5ea3103
https://www.jianshu.com/p/9f71e260925d
```



```
第一步： 在Github上创建自己的repository

第二步：建立本地仓库cd到你的本地项目根目录下，执行git命令

1：$ cd 到你的项目目录下

2：$ git init

第三步：将本地项目工作区的所有文件添加到暂存区

3：$ git add . 

第三步：将暂存区的文件提交到本地仓库

4：$ git commit -m "注释"

第五步：将本地仓库关联到Github上

5：$ git remote add origin https://github.com/zhibinhsu/ShowAllLabel.git  用自己的url（创建的仓库的地址，赋值地址栏里面的地址即可）

这步骤如果提示错误：fatal: remote origin already exists. 解决办法如下：

　　1、先删除远程 Git 仓库 $ git remote rm origin 

　　2、再重新添加远程 Git 仓库 $ git remote add origin https://github.com/zhibinhsu/ShowAllLabel.git  用自己的url（创建的仓库的地址，赋值地址栏里面的地址即可）

 

同步到服务器

6：$ git push -f origin master
```

