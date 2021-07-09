---
title: hexo yilia 主题点击左侧链接时打开空白页面问题 
permalink: yilia-issue
date: 2019-12-17 17:30
tags: hexo
---

解决办法：修改 ```themes/yilia/layout/_partial/post/left-col.ejs``` 文件中 ```<nav class="header-smart-menu">```。

<!--more-->

搜索 header-smart-menu

```js
//修改前
<nav class="header-smart-menu">
    		<% for (var i in theme.smart_menu){ %>
    			<% if(theme.smart_menu[i]){ %>
    			<a q-on="click: openSlider(e, '<%-i%>')" href="javascript:void(0)"><%= theme.smart_menu[i] %></a>
    			<% } %>
            <%}%>
		</nav>
```

```javascript
//修改后 添加 target="_self" 属性
//如果原本是 target="_blank" 则将这个属性去掉
<nav class="header-smart-menu">
    		<% for (var i in theme.smart_menu){ %>
    			<% if(theme.smart_menu[i]){ %>
    			<a q-on="click: openSlider(e, '<%-i%>')" target="_self" href="javascript:void(0)"><%= theme.smart_menu[i] %></a>
    			<% } %>
            <%}%>
		</nav>
```

