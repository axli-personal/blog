---
title: "Hugo主题定制"
date: 2022-01-05T20:22:32+08:00
tags: []
categories: []
summary: "一些Hugo主题定制相关的整理: Paginator的使用技巧"
---

## where函数的使用

```html
<!-- slice中的对象的指定field和parameter进行指定operator, 根据返回值来过滤. -->
{{ where slice field operator parameter }}
```

## 获取分页器(Paginator)

我们使用`.Paginate`获取分页器, 通常后面接一些过滤后的`Page`切片.

```html
{{ $paginator := .Paginate (where .Site.RegularPages "Type" "==" "posts") }}
```

`.Paginate`接一些复杂的表达式需要加上括号, 具体逻辑就可以自己定制了.

## 使用分页器(Paginator)

```html
{{ range $paginator.Pages }}
{{ end }}
```
