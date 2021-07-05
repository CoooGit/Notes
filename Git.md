## 常用Git操作

#### 1 仓库管理

**克隆远程仓库**

```
$ git clone ssh链接
```

------

**将一个已存在的本地目录与远程仓库关联**

初始化本地仓库：

```
$ git init
```

链接到远程仓库：

```
$ git remote add origin ssh链接
```

拉取远程仓库内容：

```
$ git pull --rebase origin master
```

将本地` master `分支与远程` master `分支关联：

```
$ git branch --set-upstream-to=origin/master
```

或使用：

```
$ git push -u origin master
```

通过` -u `参数自动与远程` master `分支绑定，` -u `参数的作用是，当尝试push一个本地分支时，自动将其与push的目标远程分支绑定，若不存在对应的远程分支，则会自动创建。

------

**删除本地仓库**

s

#### 2 分支管理

------

**创建本地分支` xxx `**

```
$ git branch xxx
```

------

**查看分支**

查看本地分支

```
$ git branch
```

查看远程分支

```
$ git branch -r
```

查看所有分支

```
$ git branch -a
```

所处的分支前以` * `标记。

查看当前所处分支的信息

```
$ git branch -vv
```



------

**切换到本地` xxx `分支**

```
$ git checkout xxx
```

------

**创建并切换到本地` xxx `分支**

```
$ git checkout -b xxx
```

该命令相当于：

```
$ git branch xxx
$ git checkout xxx
```

------

**将本地` xxx `分支与远程分支` yyy `同步**

```
git checkout -b xxx yyy
```

若本地分支` xxx `不存在，则会被自动创建；若远程分支` yyy `不存在，则会报错。

------

**重命名分支**

```
$ git branch -m oldname newname
```



#### 3 提交管理

commit

修改commit内容

