# Git Workflow

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



### 检查当前HEAD指针、显示分支情况

```console
$ git log --oneline --graph --all
```

## branch workflow

### branch deleting 删除分支

命令：

```sh
$ git branch -d del_branch
```



### branch merge 分支合并

