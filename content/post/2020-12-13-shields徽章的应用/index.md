---
title:      shields徽章的应用
description:   shields + substats 打造github上好看的小徽章
date:       2020-12-13 00:00:00+0000
image: post-bg-201213.jpg
categories:
    - github projects
tags:
    
---
## 效果

![image-20201213213608993](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20201213213608993.png)

## 官方地址

[https://shields.io/](https://shields.io/)

以下只做一下简单介绍，更多信息参考官方介绍。

## 静态徽章

格式如下：

```html
https://img.shields.io/badge/<LABEL>-<MESSAGE>-<COLOR>
```

如`https://img.shields.io/badge/Main%20Language-C++-green`，用图片格式引用，效果如下：

```
![](https://img.shields.io/badge/Main%20Language-C++-green)
```

![](https://img.shields.io/badge/Main%20Language-C++-green)

`LABEL`此处为"Main Language"，显示为徽章的左侧文字；
`MESSAGE`此处为"C++"，显示为徽章的右侧文字；
`COLOR`此处为"green"，为徽章的右侧颜色。徽章左侧默认为黑色。

## 动态徽章

格式如下：（可以引用json、xml和yaml格式的文件，下面的是json）

```html
https://img.shields.io/badge/dynamic/json?url=<URL>&label=<LABEL>&query=<$.DATA.SUBDATA>&color=<COLOR>&prefix=<PREFIX>&suffix=<SUFFIX>
```

动态徽章可以实现更多的功能，比如要显示github的粉丝数，可以借由工具让其自动更新显示，不再需要手动更改数据。

如：

```html
[![GitHub](https://img.shields.io/badge/dynamic/json?logo=github&label=GitHub&labelColor=495867&color=blue&query=%24.data.totalSubs&url=https%3A%2F%2Fapi.spencerwoo.com%2Fsubstats%2F%3Fsource%3Dgithub%26queryKey%3DTetsuOu&suffix=%20followers)](https://github.com/TetsuOu)
```

效果如下：
[![GitHub](https://img.shields.io/badge/dynamic/json?logo=github&label=GitHub&labelColor=495867&color=blue&query=%24.data.totalSubs&url=https%3A%2F%2Fapi.spencerwoo.com%2Fsubstats%2F%3Fsource%3Dgithub%26queryKey%3DTetsuOu&suffix=%20followers)](https://github.com/TetsuOu)

`logo`参数可以在左侧增添一个logo，支持的logo在[https://simpleicons.org/](https://simpleicons.org/)上。那要是该网站上没有所需要的logo呢？那就需要利用图片的base64编码了，后文再提。

`labelColor`参数设置徽章左侧颜色。

`color`参数设置徽章右侧颜色。

`query`参数设置引用数据的相对路径。

`url`参数设置数据来源。

`suffix`参数设置数据后方的文字。

### Substats

在解释上文中的url时需要先介绍个工具——Substats，官方地址：[https://substats.spencerwoo.com/](https://substats.spencerwoo.com/)

简单的说，这个工具能返回一个json文件，能得到一些需要的数据，比如github账户的followers数量等：

```http
https://api.spencerwoo.com/substats/?source=github&queryKey=TetsuOu
```

得到的数据格式为：

```json
{
	"status": 200,
	"data": {
		"totalSubs": 8,
		"subsInEachSource": {
			"github": 8
		},
		"failedSources": {}
	}
}
```

因此，上面的shields中的query参数可以填，`$.data.totalSubs`，这里即为8。

更多用法参考官网。

### CSDN徽章设置

可惜的是，上面的提到的logo网站没有CSDN的Logo，Substats也不支持CSDN的查询。只能用比较笨的方法了。

效果如下：

[![GitHub](https://img.shields.io/badge/dynamic/json?label=CSDN&labelColor=red&color=blue&query=%24.data.totalSubs&url=https://raw.githubusercontent.com/TetsuOu/TetsuOu/main/data.json&suffix=%20followers&logo=data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iVVRGLTgiIHN0YW5kYWxvbmU9Im5vIj8+CjwhRE9DVFlQRSBzdmcgUFVCTElDICItLy9XM0MvL0RURCBTVkcgMS4xLy9FTiIgImh0dHA6Ly93d3cudzMub3JnL0dyYXBoaWNzL1NWRy8xLjEvRFREL3N2ZzExLmR0ZCI+CjxzdmcgdmVyc2lvbj0iMS4xIiBpZD0iTGF5ZXJfMSIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIiB4bWxuczp4bGluaz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94bGluayIgeD0iMHB4IiB5PSIwcHgiIHdpZHRoPSIzMnB4IiBoZWlnaHQ9IjMycHgiIHZpZXdCb3g9IjAgMCAzMiAzMiIgZW5hYmxlLWJhY2tncm91bmQ9Im5ldyAwIDAgMzIgMzIiIHhtbDpzcGFjZT0icHJlc2VydmUiPiAgPGltYWdlIGlkPSJpbWFnZTAiIHdpZHRoPSIzMiIgaGVpZ2h0PSIzMiIgeD0iMCIgeT0iMCIKICAgIGhyZWY9ImRhdGE6aW1hZ2UvcG5nO2Jhc2U2NCxpVkJPUncwS0dnb0FBQUFOU1VoRVVnQUFBQ0FBQUFBZ0NBTUFBQUJFcElyR0FBQUFCR2RCVFVFQUFMR1BDL3hoQlFBQUFDQmpTRkpOCkFBQjZKZ0FBZ0lRQUFQb0FBQUNBNkFBQWRUQUFBT3BnQUFBNm1BQUFGM0NjdWxFOEFBQUJNbEJNVkVYOFZUSDhkRmY5cFpMOXhMZisKMGNmK3pzUDl1YXI4a252OFhqejhnMm4rMjlQLy8vLys5dlg5cnB6OFd6ajhXamY5dnJEKy92Nys0dHo4WlVYOFhUdisxODcrNStMOQpzSi85d0xMKzdlcjl2Szc4VmpQOXpzVCs2K2Y4aUcvOFZ6VDltWVQrOWZQK3o4WDlwSkQrNGR2OFpFUDhiMUgrNitiOGpuYjhaa1grCitQZis5UEg4YVVyOFh6MzhjRkw5c3FMOW5Zais4dS8rNyt6OFdUYjhmMlQ5dGFYOGgyMzl2N0g4WjBmK3pzVCsvZjMrMHNuKysvdjkKeTcvOXRxYjhlVno5cTVyOFlVRCsrL3I4WTBMOGJFMzhpWEg4VmpMKzFzNzhZVC84Z0dYKzlQTDhaa2IrOC9EOW00YjhZa0Q4V0RYOQp3N2IrOXZUOGhHcisrdnIrNk9QKzJkSCsxTXYrNE5yKytmZis3K3Y4Zm1QOXNLRCs4L0grN3VyOXFwbjlxcGo5d3JiK3pNSDl1NjM5Cm9ZMzhlbDc4VnpPYi9QRHVBQUFBQVdKTFIwUUxIOWZFd0FBQUFBZDBTVTFGQitRTEhnb2ZCRUczcmRnQUFBRUFTVVJCVkRqTFkyQVkKZElDUmlabUZsWTJkQTRjMEp4YzNCUER3OG1HUjVoY1E1SVlESVdFTWVSRlJrSVNZZ0xpRUpJZ2hoUzR2TFFNVWxaVURNZVVWRkxtNQpsZEFWS0FQbFZWU2hIRFYxUVEwMGVVMHRibTV0SFRoWFZ3L2RBSDJnQVFaNC9DOXZ5TTF0Wkl4SGdRblFBRk44SWNnRVZHQ0dUNEU1ClVJRUZQZ1dXM054VzB2Z1VXSE56MitDVFo3QUZXaUdDVDRFZFVJRTlQZ1hzUUFVTytCUTRPbkZ6R3pvakNiaTR1cUdxRUFBYTRlNEIKNTNwNmNZdWpLdkQyQWNXMnJ4K0k3VzhTWU1YTkhZaG1TVkF3S0prSWhvU0doVWVBMDFRSXVqTThJN21SZ1ZFVWhrTTlvbVBnMHJGeAp1dGo4d2g4VW42QmtuWmlVbkpMS01KZ0FBSzlnRy9zSHMyZGFBQUFBSlhSRldIUmtZWFJsT21OeVpXRjBaUUF5TURJd0xURXhMVE13ClZERXdPak14T2pBMEt6QXdPakF3OHVOT01nQUFBQ1YwUlZoMFpHRjBaVHB0YjJScFpua0FNakF5TUMweE1TMHpNRlF4TURvek1Ub3cKTkNzd01Eb3dNSU8rOW80QUFBQUFTVVZPUks1Q1lJST0iIC8+Cjwvc3ZnPgo=)](https://blog.csdn.net/qq_40889820)

#### 图标

首先是CSDN的图标制作。

进入网址[https://blog.csdn.net/favicon.ico](https://blog.csdn.net/favicon.ico)，获取CSDN的图标文件，`favicon.ico`

然后随便找一个可以将图片转base64的网站进行转换即可。

我这里是将其图标文件先转成`.svg`格式再编码的：

```
data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iVVRGLTgiIHN0YW5kYWxvbmU9Im5vIj8+CjwhRE9DVFlQRSBzdmcgUFVCTElDICItLy9XM0MvL0RURCBTVkcgMS4xLy9FTiIgImh0dHA6Ly93d3cudzMub3JnL0dyYXBoaWNzL1NWRy8xLjEvRFREL3N2ZzExLmR0ZCI+CjxzdmcgdmVyc2lvbj0iMS4xIiBpZD0iTGF5ZXJfMSIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIiB4bWxuczp4bGluaz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94bGluayIgeD0iMHB4IiB5PSIwcHgiIHdpZHRoPSIzMnB4IiBoZWlnaHQ9IjMycHgiIHZpZXdCb3g9IjAgMCAzMiAzMiIgZW5hYmxlLWJhY2tncm91bmQ9Im5ldyAwIDAgMzIgMzIiIHhtbDpzcGFjZT0icHJlc2VydmUiPiAgPGltYWdlIGlkPSJpbWFnZTAiIHdpZHRoPSIzMiIgaGVpZ2h0PSIzMiIgeD0iMCIgeT0iMCIKICAgIGhyZWY9ImRhdGE6aW1hZ2UvcG5nO2Jhc2U2NCxpVkJPUncwS0dnb0FBQUFOU1VoRVVnQUFBQ0FBQUFBZ0NBTUFBQUJFcElyR0FBQUFCR2RCVFVFQUFMR1BDL3hoQlFBQUFDQmpTRkpOCkFBQjZKZ0FBZ0lRQUFQb0FBQUNBNkFBQWRUQUFBT3BnQUFBNm1BQUFGM0NjdWxFOEFBQUJNbEJNVkVYOFZUSDhkRmY5cFpMOXhMZisKMGNmK3pzUDl1YXI4a252OFhqejhnMm4rMjlQLy8vLys5dlg5cnB6OFd6ajhXamY5dnJEKy92Nys0dHo4WlVYOFhUdisxODcrNStMOQpzSi85d0xMKzdlcjl2Szc4VmpQOXpzVCs2K2Y4aUcvOFZ6VDltWVQrOWZQK3o4WDlwSkQrNGR2OFpFUDhiMUgrNitiOGpuYjhaa1grCitQZis5UEg4YVVyOFh6MzhjRkw5c3FMOW5Zais4dS8rNyt6OFdUYjhmMlQ5dGFYOGgyMzl2N0g4WjBmK3pzVCsvZjMrMHNuKysvdjkKeTcvOXRxYjhlVno5cTVyOFlVRCsrL3I4WTBMOGJFMzhpWEg4VmpMKzFzNzhZVC84Z0dYKzlQTDhaa2IrOC9EOW00YjhZa0Q4V0RYOQp3N2IrOXZUOGhHcisrdnIrNk9QKzJkSCsxTXYrNE5yKytmZis3K3Y4Zm1QOXNLRCs4L0grN3VyOXFwbjlxcGo5d3JiK3pNSDl1NjM5Cm9ZMzhlbDc4VnpPYi9QRHVBQUFBQVdKTFIwUUxIOWZFd0FBQUFBZDBTVTFGQitRTEhnb2ZCRUczcmRnQUFBRUFTVVJCVkRqTFkyQVkKZElDUmlabUZsWTJkQTRjMEp4YzNCUER3OG1HUjVoY1E1SVlESVdFTWVSRlJrSVNZZ0xpRUpJZ2hoUzR2TFFNVWxaVURNZVVWRkxtNQpsZEFWS0FQbFZWU2hIRFYxUVEwMGVVMHRibTV0SFRoWFZ3L2RBSDJnQVFaNC9DOXZ5TTF0Wkl4SGdRblFBRk44SWNnRVZHQ0dUNEU1ClVJRUZQZ1dXM054VzB2Z1VXSE56MitDVFo3QUZXaUdDVDRFZFVJRTlQZ1hzUUFVTytCUTRPbkZ6R3pvakNiaTR1cUdxRUFBYTRlNEIKNTNwNmNZdWpLdkQyQWNXMnJ4K0k3VzhTWU1YTkhZaG1TVkF3S0prSWhvU0doVWVBMDFRSXVqTThJN21SZ1ZFVWhrTTlvbVBnMHJGeAp1dGo4d2g4VW42QmtuWmlVbkpMS01KZ0FBSzlnRy9zSHMyZGFBQUFBSlhSRldIUmtZWFJsT21OeVpXRjBaUUF5TURJd0xURXhMVE13ClZERXdPak14T2pBMEt6QXdPakF3OHVOT01nQUFBQ1YwUlZoMFpHRjBaVHB0YjJScFpua0FNakF5TUMweE1TMHpNRlF4TURvek1Ub3cKTkNzd01Eb3dNSU8rOW80QUFBQUFTVVZPUks1Q1lJST0iIC8+Cjwvc3ZnPgo=
```

注意shields对图片有大小限制，不超过8192 bytes。

#### 数据获取

然后是数据的获取，我这里想使用CSDN账户上的粉丝数，因为目前没找到其他工具，自己也不会写，就只能自己将json文件保存在github上：

![image-20201213223026177](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20201213223026177.png)

点击`Raw`查看源文件地址：

```
https://raw.githubusercontent.com/TetsuOu/TetsuOu/main/data.json
```

最终打造成了“伪 动态徽章”哈哈，每次只能自己手动更新。

期待以后能有支持查询CSDN粉丝数的工具出现。

## 其他

shields还支持一些预置的一些徽章生成，比如GitHub Workflow Status、License等

### Github Workflow Status

```
![](https://img.shields.io/github/actions/workflow/status/TetsuOu/TetsuOu.github.io/deploy.yml?branch=master)
```
![](https://img.shields.io/github/actions/workflow/status/TetsuOu/TetsuOu.github.io/deploy.yml?branch=master)

```
![Github License](https://img.shields.io/github/license/TetsuOu/TetsuOu.github.io)
```
![Github License](https://img.shields.io/github/license/TetsuOu/TetsuOu.github.io)

