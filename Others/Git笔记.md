# Git 笔记

[TOC]

## Git 基础

### Git 初始化

#### Git 中的别名的创建及使用

```bash
$ git config --system alias.ci commit
$ git ci -m "xxxxx"
```

#### Git 仓库初始化

```bash
$ git init
```

`git init` 命令在工作区根目录下创建了隐藏的**版本库**目录 `.git`。

#### Git 仓库中的 grep

```bash
$ git greap "XXX"
```

能够更好地搜索工作区的内容而免受版本库 `.git` 目录的影响。

#### 版本库定位

实际上，在 Git 工作区的子目录下执行操作时，会对目录**向上递归地查找** `.git` 目录（可以通过追踪执行 `git status` 命令执行时的磁盘访问来验证），也就是工作区对应的版本库，`.git` 所在的目录就是工作区的根目录。

Git 提供了寻找上述目录位置的底层命令。

- 显示版本库 .git 目录所在位置。

  ```bash
  $ git rev-parse --git-dir
  ```

- 显示工作区根目录。

  ```bash
  $ git rev-parse --show-toplevel
  ```

- 显示从当前目录位置（cd）后退（up）到工作区根目录的深度。

  ```bash
  $ git rev-parse --show-cdup
  ```

#### git config 命令参数的理解

`git config` 命令中，有的使用了 `--globa` 参数，有的使用了 `--system` 参数。

```bash
$ git config -e			#对仓库中的本地 git 配置文件进行编辑。workdir/.git/config 文件。
$ git config -e --global	#对用户目录下的 git 配置文件进行编辑。~/.gitconfig 文件。
$ git config -e --system	#对系统配置中的 git 配置文件进行编辑。一般是 、etc/gitconfig 文件。
```

上述三个配置文件从下至上优先级依次增加，优先级高的配置覆盖优先级低的 Git 环境配置。

命令 `git config` 可以用于读取和更改 INI 配置文件的内容。

```bash
$ git config core.bare
$ git config a.b something
$ git config --unset --global user.name #删除用户名
```

实际上 `git config` 命令可以用于操作任何 INI 文件，加上 `GIT_CONFIG` 变量即可。

```bash
$ GIT_CONFIG=test.ini git config a.b.c.d "hello, world" 	#添加配置
$ GIT_CONFIG=test.ini git config a.b.c.d					#读取配置
```

### Git 暂存区

#### Git 文件状态

使用命令 `git status -s` 命令后文件名前会有文件状态标志。

```bash
$ git status -s
MM helloworld.txt
```

- 第一个 M 标识了版本库和暂存区中的文件相比有改动。
- 第二个 M 表示了工作区和暂存区中的文件相比有改动。

