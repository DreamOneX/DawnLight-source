---
title: "使用 Todo.txt"
description: "If you want to get it done, first write it down."
date: 2023-01-11T00:30:03+08:00
slug: "use-todo-txt"
image: "yes.png"
math: true
categories:
    - code
tags:
    - todo
    - linux
draft: false

---

# todo.sh

> 想完成一件事，最好先把它加进 ToDo List ([GTD](http://en.wikipedia.org/wiki/Getting_Things_Done'))

## 缘起何方

[`todo.txt`](http://todotxt.org/) 是 [ginatrapani](https://ginatrapani.org) 编写的一个基于 cli 的简易 todo list 管理工具。你可以在命令行下对 `~/.todo/todo.txt` 做任何你能想到的事（当然也可以是 `done.txt` 或者其他文件），比如添加，修改，删除或者完成。

## 我怎么做

例如（假定你已经执行`alias t=todo.sh`)

```bash
t add @菜市场 买菜 +购物 # 添加一个 todo task
t p 1 A # 把 task 1 的优先级设为 A
t ls # 列出所有未完成的 tasks
t lsp # 列出优先级为 A, B, C 的 tasks
t do 1 # 标记完成 task 1
```

更多的使用方式可已通过`t help`查看

## 更远的路

`Todo.txt` 名扬天下，随着使用者的增加，各式各样的花式客户端也登上了舞台；由于 `todo.txt` 的简单，为维护其客户端提供了莫大的便利，无论是传统的各平台原生客户端，还是老生常谈的文本编辑器插件，少见的浏览器插件，甚至 Web UI，都出现了它的身影，选择一个适合你的客户端开始，会获得更好的效果

# todo.txt

`todo.txt` 使用纯文本(和 Markdown 一样)， ~~这意味着你可以使用大脑进行渲染~~

## 为什么用纯文本

如前文，不需要特殊的工具进行渲染是一个 **实实在在** 的优点。举个例子，无论是 *Office OpenXML* 还是 *Portable Document Format* 都是难以用脑子渲染的东西，而 *Markdown* 或 $\LaTeX$ 都是人类可读的，就不需要各种复杂的（动辄上百兆）渲染工具，也更便于和 **其他程序** 一同工作，尤其是 **在 Shell 的环境下**。

另外，如果使用纯文本，你就不必担心文件损坏 (点名批评 Microsoft Office) ~~(除非你的硬盘坏了)~~，而且易于传输和备份（你可以把你的 todo.txt 上传到 pastebin ，这绝对比粘贴一堆 HEX 上去好多了）

最重要的是，纯文本意味着 **开放**，纯文本 **不会像预先设置的规则那样** 告诉你 **你该做什么你不该做什么**，你可以自由发挥，使用自己想用的语法（你可以轻易的通过 *管道* 和 *sed* (或其他命令) 实现对特定语法的解析，当然，也包括直接使用或开发新的 *插件*[^1] 或 *客户端*）

[^1]: 从 [Github](https://github.com/todotxt/todo.txt-cli/wiki/Creating-Add-ons:-Examples) 获取更多信息

## 语法

![todo.txt 语法](rules.svg)

~~很多时候，解决问题只需要一张图~~

> **💡Tips:** 请注意这只是一个推荐的方式，完全不影响你自由发挥去决定如何使用 @ 或 + 或 key-value 或其他东西

### Priority - 优先级

> **⚠️Warning:** 很多应用程序会丢弃以 `(A-Z)` 形式保留的优先级，如需保留，可使用键值对的形式，如 `pri:A`

todo list 里的 Context Tag 和 Project Tag 应该能告诉你现在最重要的事情是什么，但不排除在某些情况下需要提高某件事情的优先级(当然你也可以为每件事情都分配优先级，确保能清楚的明白现在去干什么)，这时你可以通过 `(A-Z)` 的形式给出某件事情的优先级，使他们出现在`t ls`或类似命令的顶部

### Project Tag - 项目标签

如果有一个特大项目需要完成，GTD 的最好方式一定是把每一个需要完成的小项目罗列出来，逐步去完成每个小项目。todo list 应该告诉明确地告诉你下一步做什么，因此，一个 todo task 的周期不易过长，否则你就会和没有 todo list 一样手足无措。打个比方，`完成数学习题册` 和 `完成这本数学习题册的 p42 面` 相比，显然后者是一个更合乎逻辑的 todo task，因为完成一面练习需要的时间是比较合理的，而完成整本数学习题册可能需要几天。

> **💡Tips:** 一个 todo task 的周期也不易过短，如果这是你可以在两分钟之内完成的任务，你没有必要将它加入 todo list，除非这个 task 需要在特定时间完成。

所以在将某一特定项目的所有子任务加入 todo list 的时候，可以使用 `+<str>` 进行标记，比如 `+数学习题`

再次强调，这并不意味着 `+` 只能表示一种意思，你完全可以使用 `+<任务量>` 的形式告诉自己任务量有多大，以方便自己合理的规划时间。再比如，你可以用 `+` 标记一个 task 是另一个 task 的子任务

### Context Tag - 上下文标签

上下文，即一个 task 进行的地点，时间和参与者等信息，在 todo list 中使用 context tag 可以帮助你确定到什么地方干什么事，比如你打开邮箱的时候可以查看一下 `@email` 相关的 task，避免重复操作。如果今天会见到某个人，（比如他叫 kevin），你可以查看一下@kevin 相关的 task 来检查有什么要做的。
{{< details summary="" >}}
<s>怎么这么怪啊</s>
{{< /details >}}

### Creation Date - 创建日期

~~它可以告诉你，你白蓝了多久~~

### Completion Date - 完成日期

> **⚠️Warning:** 建议在添加 `x` 的时候添加完成日期，以防和创建日期混淆

(除非手写 todo.txt 不然几乎不用关心这点，几乎所有应用程序都会自动追加完成日期)

### x - 完成

一个以 **小写的 `x` 加上一个空格** 开头的 task 被标记为完成

### key/value tag - 键值对

形如 `key:value` 的东西被称为键值对，有一些常用的键值对如下

| key/value      | 含义                          |
| -------------- | --------------------------- |
| t:2023-01-25   | 在 2023.1.25 之后才需要完成         |
| due:2023-02-06 | 2023.2.6 是这个 task 的 ddl[^2] |
| pri: A         | 前文已经提到,这是表示优先级的方式           |

[^2]: Dead Line，死亡线，意思是一个 task 必须完成的日期，比如寒假作业必须在报道那天晚上（或更早）创造奇迹（也可以循序渐进）

建议多用 `t:----` tag 阻止一个 task 在无法做或者完全不需要做之前来烦你；少用 `due:----` tag，除非它真的是 ddl

# so easy[^3] ?

[^3]: 其实这里最好用 Simple

> Keep It Simple, Stupid

和 Arch 一样，todo.txt 只把最简单的东西提供给用户，剩下的由用户自己动手，自由发挥，这意味着，你可以让事情变得复杂起来

因为 todo.txt 的简单，你可以轻易的把它同任何工具组合起来使用:

* 你可以编写一个简单的 nonebot2 插件，把 todo.txt 对接到任何 IM 平台(当然你可能需要写一个 Adapter)上， ~~借助 IM 提供者的网络~~，轻易的访问待办事项
* 你可以把 `~/.todo` 添加到 [rslsync](https://www.resilio.com) 的同步目录，在任何设备上查看待办事项
* 你可以把在 `polybar` 等桌面组件类的工具中显示待办事项数或待办事项
* 你可以写一个 `crontab` 每天把高优先级的待办事项发到自己的邮箱，如果你使用 Gmail 可以加一个 tag 顺便写一个简单的东西将它添加到日历
* 你可以把它集成进浏览器，编辑器甚至智能家居设备中 ( ~~比如你可以把闹铃声设成朗读最高优先级待办事项们~~ )
* 你甚至可以写一个 Web Panel 管理 todo.txt ( ~~但这听着好像有些疯狂~~ )
* 通过 `crontab` 和自定义 `key-value` 来实现周期任务
* etc.

**这就是 KISS，适合喜欢动手折腾的人，在简单中强大，在强大中简单**