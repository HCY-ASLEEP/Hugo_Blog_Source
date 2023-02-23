---
title: VIM 删除包含指定字符的行
date: 2023-02-12 02:03:47
description: 高级操作喔
tags:
    - VIM
---

```bash
# 删除包含特定字符的行，匹配删除
:% g/abc/d

# 删除不包含特定字符的行
:% v/abc/d
:% g!/abc/d
```


