---
title: "Hugo Hexo 添加音乐播放器"
date: 2023-03-13T19:26:15+08:00
tags:
    - Hexo
    - Hugo
    - HTML
---

直接上代码，就完事了

```html
<!DOCTYPE html>
<html>
<body>
<style>
.parent {
margin-top:25px;
position: relative;
}

.parent .child {
position: absolute;
left: 50%;
top: 50%;
transform: translate(-50%, -50%);
width:100%;
}
</style>
<div class="parent">
<audio controls class="child">
<source src="horse.mp3" type="audio/mpeg">
Your browser does not support the audio element.
</audio>
</div>
</body>
</html>
```

