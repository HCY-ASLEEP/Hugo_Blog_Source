---
title: Markdown 对话框
date: 2022-11-05 20:43:19
description: markdown 写对话
tags:
    - Markdown
---

**只有左右框**

![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.11.05.21.09.58.png)



```html
<div align="right">
<div style="width:47%; border-style:solid; border-width:1px; border-radius:15px">
<div style="text-align:center;margin:5%">
这是右边
</div>
</div>
</div>


<div align="left">
<div style="width:47%; border-style:solid; border-width:1px; border-radius:15px">
<div style="text-align:center;margin:5%">
这是左边
</div>
</div>
</div>
```


<br/>

**带有箭头的左右框**
![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.11.07.17.19.38.png)

<br/>

- 写法一

	```html
	<style type="text/css">
	.div-diabox{
	    width: 60%;
	    border-style: solid;
	    border-width: 1px;
	    border-radius: 16px;
	    position: relative;
	    padding:30px;
	    text-align:center
	}
	
	.div-diabox .arrow-right-out{
	    width: 0px;
	    height: 0px;
	    border-style: solid;
	    border-color: transparent transparent transparent black;
	    border-width: 10px;
	    position: absolute;
	    top: 10px;
	    right: -20px;
	}
	.div-diabox .arrow-right-in{
	    width: 0px;
	    height: 0px;
	    border-style: solid;
	    border-color: transparent transparent transparent white;
	    border-width: 10px;
	    position: absolute;
	    top: 10px;
	    right: -19px;
	}
	.div-diabox .arrow-left-out{
	    width: 0px;
	    height: 0px;
	    border-style: solid;
	    border-color: transparent black transparent transparent;
	    border-width: 10px;
	    position: absolute;
	    top: 10px;
	    left: -20px;
	}
	.div-diabox .arrow-left-in{
	    width: 0px;
	    height: 0px;
	    border-style: solid;
	    border-color: transparent white transparent transparent;
	    border-width: 10px;
	    position: absolute;
	    top: 10px;
	    left: -19px;
	}
	</style>
	
	<div align="right">
	<div class="div-diabox">
	<span class="arrow-right-out"></span>
	<span class="arrow-right-in"></span>
	右边
	</div>
	</div>
	<br/>
	
	<div align="left">
	<div class="div-diabox">
	<span class="arrow-left-out"/></span>
	<span class="arrow-left-in"></span>
	左边
	</div>
	</div>
	<br/>
	```

- 写法二

	```html
	<div align="right">
	<div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
	<span style="width: 0px; height: 0px; border-style: solid; border-color: transparent transparent transparent black; border-width: 10px; position: absolute; top: 10px; right: -20px;"></span>
	<span style="width: 0px; height: 0px; border-style: solid; border-color: transparent transparent transparent white; border-width: 10px; position: absolute; top: 10px; right: -19px"></span>
	右边
	</div></div><br/>
	
	<div align="left">
	<div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
	<span style="width: 0px; height: 0px; border-style: solid; border-color: transparent black transparent transparent; border-width: 10px; position: absolute; top: 10px; left: -20px;"></span>
	<span style="width: 0px; height: 0px; border-style: solid; border-color: transparent white transparent transparent; border-width: 10px; position: absolute; top: 10px; left: -19px;"></span>
	左边
	</div></div><br/>
	
	```
    
    

