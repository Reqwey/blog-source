---
title: 在HEXO主题Volantis中使用多彩的CheckBox
date: "2020-04-11 00:00:00"
body: [article, comments]
cover: false
top: true
icons: [fas fa-fire red]
valine:
  placeholder: 有什么感想? 发射犇犇
mathjax: true
tags:
 - Stylus
 - JS
 - Volantis
categories:
 - [Dev, hexo]
music:
  server: netease
  type: song
  id: 461347998
---

这篇文章讲述如何在Volantis主题中添加多彩的CheckBox标签

::: success
虽然该文章面向Volantis用户, 但是其它主题也大同小异, 要求是必须使用Stylus进行渲染(如果您使用的是Volantis/Material-X, 则必须要升级到<u>2.0</u>以上版本).
:::

比如这个原本就是hexo-theme-fluid里的, 感谢[@Royce](https://www.royce2003.top) 和他的[这篇文章](https://royce2003.top/posts/60394.html#%E5%8F%AF%E4%BA%A4%E4%BA%92%E5%A4%8D%E9%80%89%E6%A1%86)的帮助, 让我成功地把它引入到Volantis主题中

<!--more-->

## 用途

> 可以用来制作各种样式的列表

## 实际操作

### 开始之前

请将主题中的两个CDN关掉, 即

{% codeblock blog/themes/volantis/_config.yml lang:yaml line_number:true mark:6,7 %}
info:
  name: Volantis
  version: 'x.x.x'
  docs: https://volantis.js.org/
  cdn: # To use CDN, write 'use_cdn: true' in 'blog/_config.yml'.
    # css: blablabla
    # js: blablabla
{% endcodeblock %}

### 配置Stylus

在 `blog/themes/volantis/source/css/_layout/` 中新建 `checkbox.styl` , 内容如下

``` stylus
input[type=checkbox] + p
  display: inline !important;
input[type=radio] + p
  display: inline !important;
input[type=checkbox]
  -webkit-appearance: none;
  -moz-appearance: none;
  -ms-appearance: none;
  -o-appearance: none;
  appearance: none;
  position: relative;
  right: 0;
  bottom: 0;
  left: 0;
  height: 20px;
  width: 20px;
  transition:all .15s ease-out 0s;
  color: #fff;
  cursor: pointer;
  display: inline-block;
  margin: .4rem .2rem .4rem !important;
  outline: none;
  border-radius: 10%;
input[type=radio]
  -webkit-appearance: none;
  -moz-appearance: none;
  -ms-appearance: none;
  -o-appearance: none;
  appearance: none;
  position: relative;
  right: 0;
  bottom: 0;
  left: 0;
  height: 20px;
  width: 20px;
  transition:all .15s ease-out 0s;
  color: #fff;
  cursor: pointer;
  display: inline-block;
  margin: .4rem .2rem .4rem !important;
  outline: none;
  border-radius: 10%;
/* Checkbox */
input[type=checkbox]
  vertical-align: -0.65rem;
  &:before, &:after
    position: absolute;
    content: "";
    background: #fff;
    transition: all .2s ease-in-out;
  &:before
    left: 2px;
    top: 6px;
    width: 0;
    height: 2px;
    transform: rotate(45deg);
    -webkit-transform: rotate(45deg);
    -moz-transform: rotate(45deg);
    -ms-transform: rotate(45deg);
    -o-transform: rotate(45deg);
  &:after
    right: 9px;
    bottom: 3px;
    width: 2px;
    height: 0;
    transform: rotate(40deg);
    -webkit-transform: rotate(40deg);
    -moz-transform: rotate(40deg);
    -ms-transform: rotate(40deg);
    -o-transform: rotate(40deg);
    transition-delay: .2s;
  &:checked
    &:before
      left: 1px;
      top: 10px;
      width: 6px;
      height: 2px;
    &:after
      right: 5px;
      bottom: 1px;
      width: 2px;
      height: 14px;
  &:indeterminate
    &:before, &:after
      width: 7px;
      height: 2px;
      transform: rotate(0);
      -webkit-transform: rotate(0);
      -moz-transform: rotate(0);
      -ms-transform: rotate(0);
      -o-transform: rotate(0);
    &:before
      left: 1px;
      top: 7px;
    &:after
      right: 1px;
      bottom: 7px;
/* Radio */
input[type=radio]
  vertical-align: -0.7rem;
  border-radius: 50%;
  &:before
    content: "";
    display: block;
    width: 10px;
    height: 10px;
    border-radius: 50%;
    margin: .2rem;
    transform: scale(0);
    transition: all ease-out 250ms;
  &:checked:before
    transform: scale(1);
/* Colors */
input[type=checkbox]
  border: 2px solid #4caf50;
  &:checked, &:indeterminate
    background: #4caf50;
input[type=radio]
  border: 2px solid #4caf50;
  &:checked:before
    background: #4caf50;
input[type=checkbox].blue
  border: 2px solid #2196f3;
  &:checked, &:indeterminate
    background: #2196f3;
input[type=radio].blue
  border: 2px solid #2196f3;
  &:checked:before
    background: #2196f3;
input[type=checkbox].red
  border: 2px solid #f44336;
  &:checked, &:indeterminate
    background: #f44336;
input[type=radio].red
  border: 2px solid #f44336;
  &:checked:before
    background: #f44336;
input[type=checkbox].orange
  border: 2px solid #ffc107;
  &:checked, &:indeterminate
    background: #ffc107;
input[type=radio].orange
  border: 2px solid #ffc107;
  &:checked:before
    background: #ffc107;
```

### 配置JavaScript

> 主要是为了方便书写CheckBox

在 `blog/themes/volantis/scripts` 中新建一个 `checkbox.js`, 加入代码:

``` javascript
'use strict';

function checkbox(args) {
  args = args[0] === ',' ? args.slice(1) : args;
  args = args.join(' ').split(',');
  var text = (args[0] || '').trim();

  if (text === 'checked' || text === 'true' || text === 'false') {
    const checked = (text === 'checked' || text === 'true');
    return `<input type="checkbox" ${ checked ? 'checked="checked"' : '' }>`;
  } else {
    !text && hexo.log.warn('Checkbox text must be defined!');

    text = hexo.render.renderSync({text: text, engine: 'markdown'}).trim();

    const checked = (args[1] || '').length > 0 && args[1].trim() !== 'false';
    const inline = (args[2] || '').length > 0 && args[2].trim() !== 'false';

    return `${ !inline ? '<div>' : '' }
            <input type="checkbox" ${ checked ? 'checked="checked"' : '' }>${ text }
          ${ !inline ? '</div>' : '' }`;
  }
}

// {% cb text, checked?, inline? %}
hexo.extend.tag.register('checkbox', checkbox, { ends: false });
hexo.extend.tag.register('cb', checkbox, { ends: false });
```

### 兼容横向CheckBox

> 需要将以下代码放在**任意一个JS文件**中(条件是这个文件必须<u>每次渲染页面时都会被调用</u>)

这里以放在 `app.js` 中为例

在其最后一行加入以下代码

```javascript
$(".indeterminate").prop("indeterminate", true);
```

::: success
现在所有工作都已完成, 下面介绍使用方法
:::

## 使用方法

### 简单版

```markdown
{% cb text, checked?, inline? %}
```

> 注:
>
> 第一个参数`text`即CheckBox之后这一行的内容, **支持渲染markdown**
>
> 第二个参数可以填写`checked/true`表示渲染一个默认为选中的CheckBox, 填`false`表示不选中
>
> 第三个参数选填, 如果填了表示所有CheckBox在同一行显示

比如

``` markdown
{% cb 这个渲染了一个<u>普通的CheckBox</u>, checked %}
```

{% cb 这个渲染了一个<u>普通的CheckBox</u>, checked %}

::: danger
**注意!!!**

由于这个是我优化过的, 因此Bug很多, 有很多兼容性问题, 建议用下面的复杂版 ;)
:::

### 复杂版(HTML)

支持 各种颜色的普通CheckBox, Radio(圆形), 以及横向选中的CheckBox

{% tabs example %}

<!-- tab 写法 -->

``` markdown
<input type="checkbox" class="blue">一个未选中的蓝色普通方形复选框

<input type="checkbox" class="orange indeterminate">一个橙色的横向选中方形复选框

<input type="radio" checked="checked">一个默认颜色(绿色)的圆形选中Radio
```

<!-- tab 示例 -->

<input type="checkbox" class="blue">一个未选中的蓝色普通方形复选框

<input type="checkbox" class="orange indeterminate">一个橙色的横向选中方形复选框

<input type="radio" checked="checked">一个默认颜色(绿色)的圆形选中Radio

{% endtabs %}

谢谢
