---
title: "Python re.findall() 的巨坑"
slug: "python-findall"
date: 2022-10-02T00:23:26+08:00
categories:
   - code
tags:
   - Python
   - code
slug: "python-findall"
draft: false
---

# 起因

~~起因肯定是代码和人都不能跑~~

# 经过

## 发现问题

```python
Invalid URL "('gxxxxm.', '/xxxxxxx4')": No scheme supplied. Perhaps you meant http://('gxxxxm.', '/xxxxxxx4')?
```

然后

```python
(Pdb) b xx
(Pdb) p a_string
http://gxxxm.xxxxxx.xx/xxxxxxx4
(Pdb) p a_regex_contains_capture_group
re.compile('https?://([\\w\\-]+\\.)+[\\w\\-]+(/[\\w\\-./?%&=]*)?')
(Pdb) p a_regex_contains_capture_group.findall(a_string)
('gxxxxm.', '/xxxxxxx4')
```

## 研究问题

---

### [TL; DR](#译为人话)

如果觉得文档晦涩难懂建议直接看人话，请戳 TL; DR

---

先是懵逼，不知道过了多久才想起来`help(re.findall)`

```python
findall(pattern, string, flags=0)
    Return a list of all non-overlapping matches in the string.

    If one or more capturing groups are present in the pattern, return
    a list of groups; this will be a list of tuples if the pattern
    has more than one group.

    Empty matches are included in the result.
```

这里写道：

> **If one or more capturing groups are present in the pattern, return a list of groups; this will be a list of tuples if the pattern has more than one group.**

---

### 译为中文

[Python Docs](https://docs.python.org/zh-cn/3/library/re.html#re.findall) 中
> re.findall(pattern, string, flags=0)
>
> 返回 pattern 在 string 中的所有非重叠匹配，以字符串列表或字符串元组列表的形式。对 string 的扫描从左至右，匹配结果按照找到的顺序返回。 空匹配也包括在结果中。
>
> 返回结果取决于模式中捕获组的数量。如果没有组，返回与整个模式匹配的字符串列表。如果有且仅有一个组，返回与该组匹配的字符串列表。如果有多个组，返回与这些组匹配的字符串元组列表。非捕获组不影响结果。

---

### 译为人话

在 *Perl风格正则表达式* 中被 `()` 包裹的内容是捕获组，非捕获组包括那四个预查模式以及**由 `?:` 开头的组**。

如果存在且仅存在一个 *捕获组*，那么 `re.findall` 会输出此 *捕获组* 捕获到的字符串的列表。

如果存在多个 *捕获组*，那么 `re.findall` 就会输出 *捕获组* 的元组的列表，而不是预期的字符串列表。

---

# 结果

在每一个组里面( `i(` )的前面加上了`?:`
