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

