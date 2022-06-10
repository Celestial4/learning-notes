# Git Workflow

## SSH

### 创建ssh key

```sh
$ ssh-keygen -t ed25519 -C "your_email@example.com"
```

### 改变Git仓库url

```sh
$ git remote set-url <name> <url>
```

## Branch basic

### commit对象以及结构

当做出一次commit时，Git存储一个commit对象，这个对象中包含一个指针指向保存的内容、作者的名字、作者的email地址、comment、和指向这次commit前一个commit的指针（可能有多个，在merge分支的情况下）

Git会将所有staged文件计算一个hash值，这些称为blob，将目录下所有的blob集合起来保存为tree对象放在Git repository中，然后创建一个commit对象，对象中一个指针指向这个tree对象

![image-20220610104301227](C:\Users\pc\AppData\Roaming\Typora\typora-user-images\image-20220610104301227.png)



在之后每次commit，都会创建类似的结构

![image-20220610104518432](C:\Users\pc\AppData\Roaming\Typora\typora-user-images\image-20220610104518432.png)



### 创建branch

Git branch就是一个简单的可以移动的指向这些commit的指针。

创建一个branch就是创建一个这样的可以移动的指针：

```console
$ git branch testing
```

> 这个新的指针指向你当前的commit
>
> ![image-20220610105000697](C:\Users\pc\AppData\Roaming\Typora\typora-user-images\image-20220610105000697.png)

Git通过一个特殊的指针叫做 HEAD的指针来标记你当前工作的branch

![image-20220610105221944](C:\Users\pc\AppData\Roaming\Typora\typora-user-images\image-20220610105221944.png)



### 切换branch

命令：

```console
$ git checkout testing
```

![image-20220610105805655](C:\Users\pc\AppData\Roaming\Typora\typora-user-images\image-20220610105805655.png)

在这个branch上的commit会向前延伸，但是master分支任然在原地，如果再次切换回master分支，会产生两个影响：

* 将HEAD指向master
* 回滚工作目录中的文件到master分支所在commit的状态，这意味着在此基础上的commit会和testing分支产生分叉

![image-20220610110548949](C:\Users\pc\AppData\Roaming\Typora\typora-user-images\image-20220610110548949.png)

新版本：

切换branch

```shell
$ git switch testing
```

创建切换branch

```sh
$ git switch -c testing
```



### 查看所有分支情况

```console
$ git log --oneline --graph --all
```

## branch workflow

### branch deleting 删除分支

命令：

```sh
$ git branch -d del_branch
```

> * 删除分支时，先切换到另外的分支（HEAD指向的branch不能被删除）
>
> * 如果要删除的分支位于当前分支前（有新的commit），需要将要删除的branch合并到当前branch，然后删除；位于当前分支后的branch可以删除

### branch merge 分支合并

合并分支命令：

```sh
$ git merge hotfix
```

合并是将其他分支合并到当前分支（合并的结果是移动当前branch），因此需要先切换到要合并的branch上

#### fast-forward 向前合并

当要合并的分支  直接 位于被合并的分支后，即在同一路径上，这种情况下没有分叉，Git会直接移动当前branch到被合并branch位置

> <img src="C:\Users\pc\AppData\Roaming\Typora\typora-user-images\image-20220610143047905.png" alt="image-20220610143047905" style="zoom:50%;" />
>
> 执行命令 git merge hotfix
>
> 就会使得master直接移动到hotfix位置

接下来可以把hotfix分支删除了 git branch -d hotfix

#### 一般合并

除了上面那种情况外（两个分支不在一条链上，不互为直接祖先子孙）进行合并时，Git会考察3个commit对象，要合并的branch指向的、被合并的branch指向的、以及前二者的公共祖先commit对象

<img src="C:\Users\pc\AppData\Roaming\Typora\typora-user-images\image-20220610143928736.png" alt="image-20220610143928736" style="zoom: 80%;" />

然后Git根据这3个commit创建一个新的快照（snapshot），然后创建一个新的commit指向这个快照，这个新的commit称为merge  commit，具有两个parent

![image-20220610144124561](C:\Users\pc\AppData\Roaming\Typora\typora-user-images\image-20220610144124561.png)

最后就可以删除被合并的，没有用的分支了

#### merge conflict 合并冲突

一般情况下，合并会产生冲突（两个分支中对同一个文件中同一个部分有不同的内容），这时就会产生合并冲突。Git就不会自动的创建merge commit，这时需要手动解决冲突，解决后再次提交

### branch 管理命令（修改，查看）

#### branch detail 分支详情

```sh
$ git branch -v -a
```

> 查看所有分支，包括远程分支

#### 查看未合并的分支

```sh
$ git branch --no-merged
```

#### 查看已经合并的分支

```sh
$ git branch --merged
```

#### 更改branch 名

```sh
$ git branch (-m | -M) [<oldbranch>] <newbranch>
```

> oldbranch参数没有的话修改当前分支

## remote branch

### remote-tracking branches 远程追踪分支

remote-tracking branches是远程分支状态的引用，名称为`<remote>/<branch>`。保存在本地，且不能修改；在进行与远程git服务器通信时，Git处理这个分支，用来提示你远程仓库的状态

#### remote add 新增远程分支

```sh
$ git remote add <remote_name> <url>
```

#### git fetch同步远程分支

远程仓库的分支可能被其他人上传更新导致与本地仓库分支有了分叉。只要没有跟服务器通信，那么本地的origin/main分支就不会移动

同步命令：

```sh
$ git fetch <remote>
```

> 这个命令会从服务器下载本地没有的数据，同时更新本地origin/main分支，并且移动远程branch到最新的位置
>
> 这个命令不会merge本地和远程（不会在本地中显示出来，只会表示出origin/main 指向的位置）
>
> <img src="C:\Users\pc\AppData\Roaming\Typora\typora-user-images\image-20220610163110941.png" alt="image-20220610163110941" style="zoom:80%;" />

#### 创建从远程拉下来的分支的本地分支

git fetch拉取新URL的分支时不会在本地自动创建分支，需要手动创建一个分支来同步远程的新分支，再手动merge就可以了

```sh
$ git switch -c <new_branch> <remote/branch>
$ git merge <remote/branch>
```

### push remote

#### 本地分支追踪远程分支

设有一个远程分支origin/test

```sh
$ git branch -u origin/test
```

#### 查看本地分支追踪的远程分支

```sh
$ git branch -vv
```

###  Deleting Remote Branches 删除远程分支

```sh
$ git push origin -d <branch>
```

