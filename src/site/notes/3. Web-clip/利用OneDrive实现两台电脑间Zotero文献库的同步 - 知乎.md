---
{"dg-publish":true,"permalink":"/3-web-clip/one-drive-zotero/"}
---

# 利用OneDrive实现两台电脑间Zotero文献库的同步 - 知乎

个人主力电脑有两台，在办公室则用台式，在其它场合则用笔记本，故需要在两台电脑间同步文献。之前一直是用的 zotero 自带的 300M 云空间，高枕无忧直到某天被告知空间已满上传失败，于是琢磨着换成外部的云同步。而手上正好又有现成的 1TB 的 ondrive，就简单尝试了一下。

## 1. Mklink指令

直接将数据文件放入 OneDrive，在两台电脑上的将 zotero 的数据文件路径指定到其上，这种简单的方式被 zotero 报 warning 后，就想到了 Mklink 指令。

![](https://pic2.zhimg.com/v2-dec4bbe49fe1d76f442c1a216d16b3d9_b.jpg)

官方警告的原因：

> **Storing the Zotero data directory directly in a cloud storage folder is extremely likely to corrupt your Zotero database and should not be done.** (The same is true of most database-backed programs.) The forums contain [many](https://www.zotero.org/forum/discussion/13359/) [threads](https://www.zotero.org/forum/discussion/27900/synching-to-dropbox/) [about](https://www.zotero.org/forum/discussion/6128/dropbox-and-zotero-15-case-conflicts/) [the problems](https://www.zotero.org/forum/discussion/24593/backing-up-a-large-database-without-corrupting-it/) that users have faced with Dropbox- or Google Drive–based setups.（将 Zotero 数据目录直接存储在云存储文件夹中极有可能损坏 Zotero 数据库，因此不应该这样做。(大多数数据库支持的程序也是如此。)这些论坛包含了很多关于用户在 Dropbox 或 Google Drive 设置时遇到的问题的帖子。）

Mklink指令是window下用于创建符号链接的工具，在NTFS文件系统中可以创建目录或文件符号或硬链接。具体可以参见[mklink | Microsoft Docs](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/mklink)以及[win10下使用mklink命令给C盘软件搬家 - LifeOfCoding - 博客园 (cnblogs.com)](https://www.cnblogs.com/life-of-coding/p/10871831.html)

![](https://pic3.zhimg.com/v2-d5e336f59783e20d12817d1f80497c36_b.jpg)

## 2. Zotero的数据文件

Zotero的默认数据文件路径是"C:\\Users\\用户名\\Zotero"，文件目录如下图所示

![](https://pic2.zhimg.com/v2-c5e363fb6bc713e22585a8b681745c31_b.jpg)

其中所有文献附件全部存放在storage文件夹中，所以我们只需同步这个文件夹就行。

## 3. 创建数据文件间的链接

首先创建一台电脑上zotero数据文件和onedrive的链接关系，CMD命令（管理员权限打开）如下：

```cmd
Mklink /j "E:\OneDrive\Zotero_storage" "C:\Users\用户名\Zotero\storage"
```

这样就在OneDrive中为storage文件创建了一个名为Zotero\_storage的“入口”（该文件夹自动生成，不需要提前新建，否则会报错“当文件已存在时，无法创建该文件”）。该入口文件不占用具体的空间，对该入口文件夹的访问和内容修改都会被链接到对原始storage文件的访问和内容修改（但又不同于普通快捷方式，普通快捷方式在onedrive中无法同步相应内容）。

接着在另一台电脑上创建onedrive上Zotero\_storage文件和的本地zotero数据文件的链接关系。首先需要删除本地Zotero文件中的storage文件（路径"C:\\Users\\用户名\\Zotero\\storage" ），然后用Mklink指令创建一个onedrive中数据文件的入口替代。CMD指令如下：

```cmd
Mklink /j "C:\Users\用户名\Zotero\storage" "C:\Users\用户名\OneDrive\Zotero_storage"
```

至此实现了两台电脑间的文献库同步

![](https://pic3.zhimg.com/v2-aa34851dc94103cacfdbc405b0a9202a_b.jpg)

不足之处是偶尔批注不能同步成功，也可能是我两台电脑操作时间间隔过短，具体有待检验。（已确定是操作时间间隔过段，会导致版本冲突不能覆盖。正常使用则笔记同步很顺利）

## 简图

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">

<div class="markdown-embed-title">



</div>

==⚠  Switch to EXCALIDRAW VIEW in the MORE OPTIONS menu of this document. ⚠==


# Text Elements
Zotero cloud ^PLEDi3Rg

data ^AVh1fYRv

zotero Local in master PC ^2nYVPodO

zotero Local in slave PC ^2upcqXHR

data ^QrWrKQ1Q

attachments ^k1ZXlaVj

OneDrive master ^YE0vFE1f

Actual location
E:\OneDrive\Zotero_storage ^7adiQMQ6

Link (MkLink):
C:\Users\user\Zotero\storage ^i73QmlQu

OneDrive slave ^A8yHYGqO

Actual location
C:\Users\user\OneDrive\Zotero_storage ^d7sckug2

Link (MkLink):
C:\Users\user\Zotero\storage ^Rzx7vmWl

attachments ^03hIh8kH


# Embedded files
198ad55870cdce7b42ae93ba02e33f9fecbd457b: [[~Attachments/Pasted Image 20220323000959_014.png]]
11e7583f9c4c67d03a59d021fa327ca2bc682d63: [[~Attachments/Pasted Image 20220323000415_481.png]]

%%
# Drawing
```json
{
	"type": "excalidraw",
	"version": 2,
	"source": "https://excalidraw.com",
	"elements": [
		{
			"type": "text",
			"version": 32,
			"versionNonce": 2065538737,
			"isDeleted": false,
			"id": "YE0vFE1f",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -240,
			"y": -200,
			"strokeColor": "#5f3dc4",
			"backgroundColor": "transparent",
			"width": 229,
			"height": 35,
			"seed": 96916,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "79TKN4XVyoQ61k0RFUVKu",
					"type": "arrow"
				}
			],
			"updated": 1648282604389,
			"link": null,
			"fontSize": 28,
			"fontFamily": 1,
			"text": "OneDrive master",
			"rawText": "OneDrive master",
			"baseline": 25,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "OneDrive master"
		},
		{
			"type": "text",
			"version": 132,
			"versionNonce": 102080735,
			"isDeleted": false,
			"id": "7adiQMQ6",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -260,
			"y": -155,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 245,
			"height": 38,
			"seed": 13914,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648282604389,
			"link": null,
			"fontSize": 16,
			"fontFamily": 3,
			"text": "Actual location\nE:\\OneDrive\\Zotero_storage",
			"rawText": "Actual location\nE:\\OneDrive\\Zotero_storage",
			"baseline": 35,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Actual location\nE:\\OneDrive\\Zotero_storage"
		},
		{
			"type": "text",
			"version": 308,
			"versionNonce": 1211670673,
			"isDeleted": false,
			"id": "PLEDi3Rg",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -600,
			"y": -220,
			"strokeColor": "#2b8a3e",
			"backgroundColor": "transparent",
			"width": 180,
			"height": 35,
			"seed": 485955113,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "ystW1pN8s8RXJ4OMFWU-s",
					"type": "arrow"
				},
				{
					"id": "tTJa2-suRJeVsGafeV_Yx",
					"type": "arrow"
				}
			],
			"updated": 1648282604389,
			"link": null,
			"fontSize": 28,
			"fontFamily": 1,
			"text": "Zotero cloud",
			"rawText": "Zotero cloud",
			"baseline": 25,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Zotero cloud"
		},
		{
			"type": "rectangle",
			"version": 239,
			"versionNonce": 1837903103,
			"isDeleted": false,
			"id": "KxNbQ5mf4CbKZHRsjP73A",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -600,
			"y": -80,
			"strokeColor": "#5c940d",
			"backgroundColor": "transparent",
			"width": 126,
			"height": 60,
			"seed": 575372519,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "AVh1fYRv",
					"type": "text"
				},
				{
					"id": "F1KuZFNMdmGSU8LFsuYSR",
					"type": "arrow"
				},
				{
					"id": "tTJa2-suRJeVsGafeV_Yx",
					"type": "arrow"
				}
			],
			"updated": 1648282604389,
			"link": null
		},
		{
			"type": "text",
			"version": 235,
			"versionNonce": 953957731,
			"isDeleted": false,
			"id": "AVh1fYRv",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -595,
			"y": -67,
			"strokeColor": "#5c940d",
			"backgroundColor": "transparent",
			"width": 116,
			"height": 34,
			"seed": 400845065,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648433726154,
			"link": null,
			"fontSize": 28,
			"fontFamily": 1,
			"text": "data",
			"rawText": "data",
			"baseline": 24,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "KxNbQ5mf4CbKZHRsjP73A",
			"originalText": "data"
		},
		{
			"type": "ellipse",
			"version": 492,
			"versionNonce": 446444831,
			"isDeleted": false,
			"id": "jMPPE6UiYmPHWsmZs3vf1",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -480,
			"y": 140,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 269,
			"height": 134,
			"seed": 1803595559,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "2nYVPodO",
					"type": "text"
				},
				{
					"id": "F1KuZFNMdmGSU8LFsuYSR",
					"type": "arrow"
				},
				{
					"id": "RjWV_-7dlu_WpW-BuGxju",
					"type": "arrow"
				}
			],
			"updated": 1648282604389,
			"link": null
		},
		{
			"type": "text",
			"version": 486,
			"versionNonce": 1853915299,
			"isDeleted": false,
			"id": "2nYVPodO",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -475,
			"y": 170,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 259,
			"height": 74,
			"seed": 226654921,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648433726155,
			"link": null,
			"fontSize": 28,
			"fontFamily": 4,
			"text": "zotero Local in \nmaster PC",
			"rawText": "zotero Local in master PC",
			"baseline": 66,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "jMPPE6UiYmPHWsmZs3vf1",
			"originalText": "zotero Local in master PC"
		},
		{
			"type": "arrow",
			"version": 1445,
			"versionNonce": 1641322755,
			"isDeleted": false,
			"id": "F1KuZFNMdmGSU8LFsuYSR",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 2,
			"opacity": 100,
			"angle": 6.050040832470876,
			"x": -522.8572199111109,
			"y": -37.57394572750065,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 139.50614270477223,
			"height": 195.88370772889448,
			"seed": 866980649,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1648433726155,
			"link": null,
			"startBinding": {
				"elementId": "KxNbQ5mf4CbKZHRsjP73A",
				"gap": 1.1915135787352185,
				"focus": 0.4342538427628894
			},
			"endBinding": {
				"elementId": "jMPPE6UiYmPHWsmZs3vf1",
				"gap": 1,
				"focus": 0.3875905715007559
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					139.50614270477223,
					195.88370772889448
				]
			]
		},
		{
			"type": "arrow",
			"version": 2682,
			"versionNonce": 1176480941,
			"isDeleted": false,
			"id": "RjWV_-7dlu_WpW-BuGxju",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -140,
			"y": -20,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 180.18342910761965,
			"height": 159.69820702188258,
			"seed": 1994013097,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1648433726155,
			"link": null,
			"startBinding": null,
			"endBinding": {
				"elementId": "jMPPE6UiYmPHWsmZs3vf1",
				"gap": 1.4990979642176114,
				"focus": -0.32807708654843304
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					-180.18342910761965,
					159.69820702188258
				]
			]
		},
		{
			"type": "arrow",
			"version": 914,
			"versionNonce": 1451754477,
			"isDeleted": false,
			"id": "tTJa2-suRJeVsGafeV_Yx",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -536.9924981787424,
			"y": -85.7671957671958,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 16.964252433245292,
			"height": 95.5291005291005,
			"seed": 641618279,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1648433726154,
			"link": null,
			"startBinding": {
				"elementId": "KxNbQ5mf4CbKZHRsjP73A",
				"gap": 5.767195767195801,
				"focus": -0.09284860897764113
			},
			"endBinding": {
				"elementId": "PLEDi3Rg",
				"gap": 3.7037037037036953,
				"focus": 0.06726457399103153
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					16.964252433245292,
					-95.5291005291005
				]
			]
		},
		{
			"type": "arrow",
			"version": 1695,
			"versionNonce": 804212753,
			"isDeleted": false,
			"id": "t2GNyo4j",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -147.8311663066723,
			"y": -61.5,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 4.723830831672615,
			"height": 58.685368509963155,
			"seed": 761105543,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1648282604389,
			"link": "",
			"startBinding": null,
			"endBinding": null,
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					4.723830831672615,
					-58.685368509963155
				]
			]
		},
		{
			"type": "ellipse",
			"version": 582,
			"versionNonce": 304035633,
			"isDeleted": false,
			"id": "RRZVOgJ_Wnk7KMJjFnreQ",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 176.35338345864682,
			"y": 131.32706766917306,
			"strokeColor": "#0b7285",
			"backgroundColor": "transparent",
			"width": 269,
			"height": 134,
			"seed": 1106755113,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "2upcqXHR",
					"type": "text"
				},
				{
					"id": "F1KuZFNMdmGSU8LFsuYSR",
					"type": "arrow"
				},
				{
					"id": "RjWV_-7dlu_WpW-BuGxju",
					"type": "arrow"
				},
				{
					"id": "iT3mOvaLO9KOATLmvjN0T",
					"type": "arrow"
				}
			],
			"updated": 1648282617125,
			"link": null
		},
		{
			"type": "text",
			"version": 561,
			"versionNonce": 888415597,
			"isDeleted": false,
			"id": "2upcqXHR",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 181.35338345864682,
			"y": 163.82706766917306,
			"strokeColor": "#0b7285",
			"backgroundColor": "transparent",
			"width": 259,
			"height": 69,
			"seed": 1225348327,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648433726156,
			"link": null,
			"fontSize": 28,
			"fontFamily": 1,
			"text": "zotero Local in \nslave PC",
			"rawText": "zotero Local in slave PC",
			"baseline": 59,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "RRZVOgJ_Wnk7KMJjFnreQ",
			"originalText": "zotero Local in slave PC"
		},
		{
			"type": "rectangle",
			"version": 305,
			"versionNonce": 838830015,
			"isDeleted": false,
			"id": "3nwrOLdu_jOB5rwShHhIF",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "dashed",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 40,
			"y": -60,
			"strokeColor": "#5c940d",
			"backgroundColor": "transparent",
			"width": 126,
			"height": 81,
			"seed": 2131066055,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "QrWrKQ1Q",
					"type": "text"
				},
				{
					"id": "F1KuZFNMdmGSU8LFsuYSR",
					"type": "arrow"
				},
				{
					"id": "tTJa2-suRJeVsGafeV_Yx",
					"type": "arrow"
				},
				{
					"id": "ystW1pN8s8RXJ4OMFWU-s",
					"type": "arrow"
				},
				{
					"id": "iT3mOvaLO9KOATLmvjN0T",
					"type": "arrow"
				}
			],
			"updated": 1648282615509,
			"link": null
		},
		{
			"type": "text",
			"version": 286,
			"versionNonce": 1980853805,
			"isDeleted": false,
			"id": "QrWrKQ1Q",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 45,
			"y": -36.5,
			"strokeColor": "#5c940d",
			"backgroundColor": "transparent",
			"width": 116,
			"height": 34,
			"seed": 2075318057,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648433726157,
			"link": null,
			"fontSize": 28,
			"fontFamily": 1,
			"text": "data",
			"rawText": "data",
			"baseline": 24,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "3nwrOLdu_jOB5rwShHhIF",
			"originalText": "data"
		},
		{
			"type": "arrow",
			"version": 1246,
			"versionNonce": 1608315875,
			"isDeleted": false,
			"id": "ystW1pN8s8RXJ4OMFWU-s",
			"fillStyle": "solid",
			"strokeWidth": 4,
			"strokeStyle": "dotted",
			"roughness": 0,
			"opacity": 100,
			"angle": 0,
			"x": -501.2233257275239,
			"y": -225.38642439431888,
			"strokeColor": "#2b8a3e",
			"backgroundColor": "#ced4da",
			"width": 630.951285911876,
			"height": 229.10907247794393,
			"seed": 773700489,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1648433726156,
			"link": null,
			"startBinding": {
				"elementId": "PLEDi3Rg",
				"focus": -0.5683445595411454,
				"gap": 5.38642439431888
			},
			"endBinding": {
				"elementId": "3nwrOLdu_jOB5rwShHhIF",
				"gap": 14.524970963994804,
				"focus": 0.8258059462031366
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "triangle",
			"points": [
				[
					0,
					0
				],
				[
					362.36906072209473,
					-78.24761904761985
				],
				[
					630.951285911876,
					150.86145343032408
				]
			]
		},
		{
			"type": "rectangle",
			"version": 463,
			"versionNonce": 1652497841,
			"isDeleted": false,
			"id": "I95dVE-U4R7kPRvnIlC2w",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 340,
			"y": -100,
			"strokeColor": "#5f3dc4",
			"backgroundColor": "transparent",
			"width": 201,
			"height": 45,
			"seed": 828206215,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "k1ZXlaVj",
					"type": "text"
				},
				{
					"id": "RjWV_-7dlu_WpW-BuGxju",
					"type": "arrow"
				},
				{
					"id": "8qjhnELroVGAmHCOWOmqI",
					"type": "arrow"
				},
				{
					"id": "iT3mOvaLO9KOATLmvjN0T",
					"type": "arrow"
				},
				{
					"id": "vjrAPyWehObaqWzKXU-cb",
					"type": "arrow"
				},
				{
					"id": "QEWdUolXPJz7VzC5Gbomc",
					"type": "arrow"
				}
			],
			"updated": 1648282604390,
			"link": null
		},
		{
			"type": "text",
			"version": 448,
			"versionNonce": 1657584323,
			"isDeleted": false,
			"id": "k1ZXlaVj",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 345,
			"y": -94.5,
			"strokeColor": "#5f3dc4",
			"backgroundColor": "transparent",
			"width": 191,
			"height": 34,
			"seed": 786613097,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648433726157,
			"link": null,
			"fontSize": 28,
			"fontFamily": 1,
			"text": "attachments",
			"rawText": "attachments",
			"baseline": 24,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "I95dVE-U4R7kPRvnIlC2w",
			"originalText": "attachments"
		},
		{
			"type": "arrow",
			"version": 1742,
			"versionNonce": 1695694737,
			"isDeleted": false,
			"id": "79TKN4XVyoQ61k0RFUVKu",
			"fillStyle": "solid",
			"strokeWidth": 4,
			"strokeStyle": "dotted",
			"roughness": 0,
			"opacity": 100,
			"angle": 0,
			"x": -120,
			"y": -206.32645385275782,
			"strokeColor": "#5f3dc4",
			"backgroundColor": "#ced4da",
			"width": 443.1863860043162,
			"height": 130.3745882629712,
			"seed": 820300969,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1648282604390,
			"link": null,
			"startBinding": {
				"elementId": "YE0vFE1f",
				"focus": -0.15864336520467376,
				"gap": 6.3264538527578225
			},
			"endBinding": {
				"elementId": "A8yHYGqO",
				"focus": 0.20200531244667577,
				"gap": 1
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "triangle",
			"points": [
				[
					0,
					0
				],
				[
					149.1728112297693,
					-130.3745882629712
				],
				[
					443.1863860043162,
					-14.673546147242178
				]
			]
		},
		{
			"type": "arrow",
			"version": 709,
			"versionNonce": 1541406207,
			"isDeleted": false,
			"id": "QEWdUolXPJz7VzC5Gbomc",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 530.2588783830513,
			"y": -54,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 430.3242053530803,
			"height": 357.99999999999994,
			"seed": 802067847,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1648282604390,
			"link": null,
			"startBinding": {
				"elementId": "Rzx7vmWl",
				"focus": -1.0199381972132289,
				"gap": 14.000000000000014
			},
			"endBinding": null,
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					430.3242053530803,
					357.99999999999994
				]
			]
		},
		{
			"type": "arrow",
			"version": 588,
			"versionNonce": 75136899,
			"isDeleted": false,
			"id": "iT3mOvaLO9KOATLmvjN0T",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 1.7832800941364138,
			"x": 221.0129788830345,
			"y": 40.527502224763616,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 81.37907667901507,
			"height": 99.09805680371267,
			"seed": 949279817,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1648433726156,
			"link": null,
			"startBinding": {
				"elementId": "RRZVOgJ_Wnk7KMJjFnreQ",
				"gap": 8.162414429135808,
				"focus": -0.3079899424519386
			},
			"endBinding": {
				"elementId": "3nwrOLdu_jOB5rwShHhIF",
				"gap": 18.852776534099725,
				"focus": 0.10120172543418444
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					-81.37907667901507,
					99.09805680371267
				]
			]
		},
		{
			"type": "text",
			"version": 157,
			"versionNonce": 273540639,
			"isDeleted": false,
			"id": "i73QmlQu",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "dotted",
			"roughness": 2,
			"opacity": 100,
			"angle": 0,
			"x": -260,
			"y": 0,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 263,
			"height": 38,
			"seed": 1374181806,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648282604390,
			"link": null,
			"fontSize": 16,
			"fontFamily": 3,
			"text": "Link (MkLink):\nC:\\Users\\user\\Zotero\\storage",
			"rawText": "Link (MkLink):\nC:\\Users\\user\\Zotero\\storage",
			"baseline": 35,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Link (MkLink):\nC:\\Users\\user\\Zotero\\storage"
		},
		{
			"type": "text",
			"version": 53,
			"versionNonce": 1350717265,
			"isDeleted": false,
			"id": "A8yHYGqO",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 240,
			"y": -220,
			"strokeColor": "#5f3dc4",
			"backgroundColor": "transparent",
			"width": 200,
			"height": 34,
			"seed": 1939663026,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "79TKN4XVyoQ61k0RFUVKu",
					"type": "arrow"
				},
				{
					"id": "vjrAPyWehObaqWzKXU-cb",
					"type": "arrow"
				}
			],
			"updated": 1648282604390,
			"link": null,
			"fontSize": 27.317073170731707,
			"fontFamily": 1,
			"text": "OneDrive slave",
			"rawText": "OneDrive slave",
			"baseline": 24,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "OneDrive slave"
		},
		{
			"type": "arrow",
			"version": 355,
			"versionNonce": 930947213,
			"isDeleted": false,
			"id": "vjrAPyWehObaqWzKXU-cb",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 344.6479145351844,
			"y": -182.65861719805335,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 75.41685797827427,
			"height": 76.23055323493926,
			"seed": 395464370,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1648433726157,
			"link": null,
			"startBinding": {
				"elementId": "A8yHYGqO",
				"gap": 3.3413828019466507,
				"focus": 0.13248189568337546
			},
			"endBinding": {
				"elementId": "I95dVE-U4R7kPRvnIlC2w",
				"gap": 6.428063963114056,
				"focus": 0.06666720108038124
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					75.41685797827427,
					76.23055323493926
				]
			]
		},
		{
			"type": "text",
			"version": 174,
			"versionNonce": 989740337,
			"isDeleted": false,
			"id": "d7sckug2",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 400,
			"y": -180,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 348,
			"height": 38,
			"seed": 1603283570,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648282604390,
			"link": null,
			"fontSize": 16,
			"fontFamily": 3,
			"text": "Actual location\nC:\\Users\\user\\OneDrive\\Zotero_storage",
			"rawText": "Actual location\nC:\\Users\\user\\OneDrive\\Zotero_storage",
			"baseline": 35,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Actual location\nC:\\Users\\user\\OneDrive\\Zotero_storage"
		},
		{
			"type": "text",
			"version": 218,
			"versionNonce": 687090271,
			"isDeleted": false,
			"id": "Rzx7vmWl",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "dotted",
			"roughness": 2,
			"opacity": 100,
			"angle": 0,
			"x": 280,
			"y": -40,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 263,
			"height": 38,
			"seed": 2024736302,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "QEWdUolXPJz7VzC5Gbomc",
					"type": "arrow"
				}
			],
			"updated": 1648282604390,
			"link": null,
			"fontSize": 16,
			"fontFamily": 3,
			"text": "Link (MkLink):\nC:\\Users\\user\\Zotero\\storage",
			"rawText": "Link (MkLink):\nC:\\Users\\user\\Zotero\\storage",
			"baseline": 35,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Link (MkLink):\nC:\\Users\\user\\Zotero\\storage"
		},
		{
			"type": "image",
			"version": 38,
			"versionNonce": 239824291,
			"isDeleted": false,
			"id": "1RPfolFw",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 160,
			"y": 320,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 423.3473980309423,
			"height": 500,
			"seed": 51192,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648433733878,
			"link": null,
			"status": "pending",
			"fileId": "198ad55870cdce7b42ae93ba02e33f9fecbd457b",
			"scale": [
				1,
				1
			]
		},
		{
			"type": "image",
			"version": 31,
			"versionNonce": 383616639,
			"isDeleted": false,
			"id": "zxhsYtvj",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 700,
			"y": 300,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 427.9210925644917,
			"height": 500.00000000000006,
			"seed": 36958,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648282604390,
			"link": null,
			"status": "pending",
			"fileId": "11e7583f9c4c67d03a59d021fa327ca2bc682d63",
			"scale": [
				1,
				1
			]
		},
		{
			"type": "rectangle",
			"version": 465,
			"versionNonce": 1954768113,
			"isDeleted": false,
			"id": "QHz-osZXMgu933ItHP8KV",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -280,
			"y": -60,
			"strokeColor": "#5f3dc4",
			"backgroundColor": "transparent",
			"width": 201,
			"height": 45,
			"seed": 824980479,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "03hIh8kH",
					"type": "text"
				},
				{
					"id": "RjWV_-7dlu_WpW-BuGxju",
					"type": "arrow"
				},
				{
					"id": "8qjhnELroVGAmHCOWOmqI",
					"type": "arrow"
				},
				{
					"id": "iT3mOvaLO9KOATLmvjN0T",
					"type": "arrow"
				},
				{
					"id": "vjrAPyWehObaqWzKXU-cb",
					"type": "arrow"
				},
				{
					"id": "QEWdUolXPJz7VzC5Gbomc",
					"type": "arrow"
				}
			],
			"updated": 1648282604390,
			"link": null
		},
		{
			"type": "text",
			"version": 449,
			"versionNonce": 159614701,
			"isDeleted": false,
			"id": "03hIh8kH",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -275,
			"y": -54.5,
			"strokeColor": "#5f3dc4",
			"backgroundColor": "transparent",
			"width": 191,
			"height": 34,
			"seed": 1843416945,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648433726158,
			"link": null,
			"fontSize": 28,
			"fontFamily": 1,
			"text": "attachments",
			"rawText": "attachments",
			"baseline": 24,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "QHz-osZXMgu933ItHP8KV",
			"originalText": "attachments"
		}
	],
	"appState": {
		"theme": "light",
		"viewBackgroundColor": "#ffffff",
		"currentItemStrokeColor": "#000000",
		"currentItemBackgroundColor": "transparent",
		"currentItemFillStyle": "hachure",
		"currentItemStrokeWidth": 2,
		"currentItemStrokeStyle": "solid",
		"currentItemRoughness": 1,
		"currentItemOpacity": 100,
		"currentItemFontFamily": 4,
		"currentItemFontSize": 16,
		"currentItemTextAlign": "left",
		"currentItemStrokeSharpness": "sharp",
		"currentItemStartArrowhead": null,
		"currentItemEndArrowhead": "arrow",
		"currentItemLinearStrokeSharpness": "round",
		"gridSize": 20,
		"colorPalette": {}
	},
	"files": {}
}
```
%%
</div></div>


## 4. 其他参考

1.  [sync[Zotero Documentation]](https://www.zotero.org/support/sync)
2.  [Zotero 同步 Onedrive - 123456 的文章 - 知乎](https://zhuanlan.zhihu.com/p/31869164)