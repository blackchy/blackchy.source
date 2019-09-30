title: Hexo Next 修改超链接颜色
date: 2019-9-30 00:00:00
categories: misc
tags: [next]
---

Hexo Next自带的超链接颜色为灰色加下划线，看起来不是特别明显，所以将其修改为蓝色。

<!-- more -->

1.在`./themes/next/source/css/_custom/custom.styl`里添加如下代码。

```
if hexo-config("custom_css.post_body_a.enable")
  .post-body 
    a:not(.btn){
      color: convert(hexo-config("custom_css.post_body_a.normal_color"));
      border-bottom: none;
      &:hover {
        color: convert(hexo-config("custom_css.post_body_a.hover_color"));
        text-decoration: underline;
      }
    }
```
`a:not(.btn)`避免修改首页的"阅读全文"按钮

2.在`./themes/next/_config.yml`里添加如下代码。

```
custom_css:
  post_body_a:
    enable: true
    normal_color: "#3296FA"
    hover_color: "#0593d3"
```