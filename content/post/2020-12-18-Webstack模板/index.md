---
title:      Webstack模板
description:   webstack 自定义网址导航模板
date:       2020-12-18 
image: post-bg-201218.png
categories:
    - github projects
tags:
    
---

## 效果

[https://TetsuOu.github.io/SiteNavigation/](https://TetsuOu.github.io/SiteNavigation/)

![](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20201218160614522.png)

## 官方地址

[https://github.com/WebStackPage/WebStackPage.github.io](https://github.com/WebStackPage/WebStackPage.github.io)

## 开始

官方地址给出了除**静态托管**方式以外的使用方式的链接。

目前我只试用了静态托管方式，那就是选择`clone`或`fork`之后，主要对`index.html`文件进行增删改，然后部署到Github Pages上。

可以用来熟悉前端语言。

## 注意事项

### 锚点设置

```html
<li>
	<a href="#Recommended" class="smooth">
		<i class="linecons-star"></i>
        <span class="title">Recommended</span>
    </a>
</li>
```

![image-20201216162619038](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20201216162619038.png)

```html
<h4 class="text-gray"><i class="linecons-tag" style="margin-right: 7px;" id="Recommended"></i>Recommended</h4>
	<div class="row">
		<div class="col-sm-3">
			<div class="xe-widget xe-conversations box2 label-info" onclick="window.open('https://www.wikipedia.org/', '_blank')" data-toggle="tooltip" data-placement="bottom" title="" data-original-title="https://dribbble.com/">
            	<div class="xe-comment-entry">
                	<a class="xe-user-img">
                    	<img data-src="../assets/images/logos/wekipedia.ico" class="lozad img-circle" width="40">
                    </a>
                <div class="xe-comment">
                    <a href="#" class="xe-user-name overflowClip_1">
                    	<strong>WikiPedia</strong>
                    </a>
                    <p class="overflowClip_2">自由的百科全书</p>
                </div>
          	</div>
		</div>
	</div>
</div>
```

需要自己修改的就是 **\<i>** 标签里的锚点、**\<img>** 标签里的logo图片、**onclick** 属性里的网址、**data-original-title** 属性里的网址、**\<strong>** 标签里的网站名称和 **\<p>** 标签里的网站介绍。

### 标签设置

注意一行的标签设置的是4个。

![image-20201216164019939](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20201216164019939.png)

```html
<h4 class="text-gray"><i class="linecons-tag" style="margin-right: 7px;" id="Recommended"></i>Recommended</h4>
	<div class="row">
        <div class="col-sm-3">
            ...
        </div>
        <div class="col-sm-3">
            ...
        </div>
        <div class="col-sm-3">
            ...
        </div>
        <div class="col-sm-3">
            ...
        </div>
	</div>
	<div class="row">	<!-- 注意有多余4个标签时，要多起一个<div class="row"> -->
        <div class="col-sm-3">
            ...
        </div>
        <div class="col-sm-3">
            ...
        </div>
        <div class="col-sm-3">
            ...
        </div>
	</div>
```

### linecons

这里左侧的图标可以在`assets\css\fonts\linecons\css\linecons-codes.css`中查询支持的icon

![image-20201216170432246](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20201216170432246.png)

