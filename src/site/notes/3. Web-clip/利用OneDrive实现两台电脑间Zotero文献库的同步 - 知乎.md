---
{"dg-publish":true,"permalink":"/3-web-clip/one-drive-zotero/"}
---

# 利用OneDrive实现两台电脑间Zotero文献库的同步 - 知乎

个人主力电脑有两台，在办公室则用台式，在其它场合则用笔记本，故需要在两台电脑间同步文献。之前一直是用的 zotero 自带的 300M 云空间，高枕无忧直到某天被告知空间已满上传失败，于是琢磨着换成外部的云同步。而手上正好又有现成的 1TB 的 ondrive，就简单尝试了一下。

## 1. Mklink指令

直接将数据文件放入 OneDrive，在两台电脑上的将 zotero 的数据文件路径指定到其上，这种简单的方式被 zotero 报 warning 后，就想到了 Mklink 指令。

![](https://pic2.zhimg.com/v2-dec4bbe49fe1d76f442c1a216d16b3d9_b.jpg)

官方警告的原因：
>  *Storing the Zotero data directory directly in a cloud storage folder is extremely likely to corrupt your Zotero database and should not be done.* (The same is true of most database-backed programs.) The forums contain [many](https://www.zotero.org/forum/discussion/13359/) [threads](https://www.zotero.org/forum/discussion/27900/synching-to-dropbox/) [about](https://www.zotero.org/forum/discussion/6128/dropbox-and-zotero-15-case-conflicts/) [the problems](https://www.zotero.org/forum/discussion/24593/backing-up-a-large-database-without-corrupting-it/) that users have faced with Dropbox- or Google Drive–based setups.（将 Zotero 数据目录直接存储在云存储文件夹中极有可能损坏 Zotero 数据库，因此不应该这样做。(大多数数据库支持的程序也是如此。)这些论坛包含了很多关于用户在 Dropbox 或 Google Drive 设置时遇到的问题的帖子。）

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
			"version": 82,
			"versionNonce": 194658943,
			"isDeleted": false,
			"id": "YE0vFE1f",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 500,
			"y": -403.298957884271,
			"strokeColor": "#5f3dc4",
			"backgroundColor": "transparent",
			"width": 178,
			"height": 34,
			"seed": 96916,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "79TKN4XVyoQ61k0RFUVKu",
					"type": "arrow"
				}
			],
			"updated": 1649216711447,
			"link": null,
			"fontSize": 28,
			"fontFamily": 4,
			"text": "OneDrive master",
			"rawText": "OneDrive master",
			"baseline": 28,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "OneDrive master"
		},
		{
			"type": "text",
			"version": 184,
			"versionNonce": 1052326367,
			"isDeleted": false,
			"id": "7adiQMQ6",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 480,
			"y": -358.298957884271,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 245,
			"height": 38,
			"seed": 13914,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1649226332326,
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
			"version": 358,
			"versionNonce": 1992827601,
			"isDeleted": false,
			"id": "PLEDi3Rg",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 140,
			"y": -423.298957884271,
			"strokeColor": "#2b8a3e",
			"backgroundColor": "transparent",
			"width": 129,
			"height": 34,
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
			"updated": 1649216711447,
			"link": null,
			"fontSize": 28,
			"fontFamily": 4,
			"text": "Zotero cloud",
			"rawText": "Zotero cloud",
			"baseline": 28,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Zotero cloud"
		},
		{
			"type": "rectangle",
			"version": 273,
			"versionNonce": 342802143,
			"isDeleted": false,
			"id": "KxNbQ5mf4CbKZHRsjP73A",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 140,
			"y": -283.298957884271,
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
			"updated": 1649216711447,
			"link": null
		},
		{
			"type": "text",
			"version": 288,
			"versionNonce": 1417087249,
			"isDeleted": false,
			"id": "AVh1fYRv",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 145,
			"y": -271.798957884271,
			"strokeColor": "#5c940d",
			"backgroundColor": "transparent",
			"width": 116,
			"height": 37,
			"seed": 400845065,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1649226326885,
			"link": null,
			"fontSize": 28,
			"fontFamily": 4,
			"text": "data",
			"rawText": "data",
			"baseline": 30,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "KxNbQ5mf4CbKZHRsjP73A",
			"originalText": "data"
		},
		{
			"type": "ellipse",
			"version": 527,
			"versionNonce": 1870881727,
			"isDeleted": false,
			"id": "jMPPE6UiYmPHWsmZs3vf1",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 260,
			"y": -63.298957884270976,
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
				}
			],
			"updated": 1649226275221,
			"link": null
		},
		{
			"type": "text",
			"version": 537,
			"versionNonce": 851354783,
			"isDeleted": false,
			"id": "2nYVPodO",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 265,
			"y": -33.298957884270976,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 259,
			"height": 74,
			"seed": 226654921,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1649226326885,
			"link": null,
			"fontSize": 28,
			"fontFamily": 4,
			"text": "zotero Local in master \nPC",
			"rawText": "zotero Local in master PC",
			"baseline": 67,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "jMPPE6UiYmPHWsmZs3vf1",
			"originalText": "zotero Local in master PC"
		},
		{
			"type": "arrow",
			"version": 1619,
			"versionNonce": 897777233,
			"isDeleted": false,
			"id": "F1KuZFNMdmGSU8LFsuYSR",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 2,
			"opacity": 100,
			"angle": 6.050040832470876,
			"x": 217.36114510789452,
			"y": -240.6585692833534,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 139.3365401952248,
			"height": 195.645053285788,
			"seed": 866980649,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1649230300677,
			"link": null,
			"startBinding": {
				"elementId": "KxNbQ5mf4CbKZHRsjP73A",
				"gap": 1.3830271574704511,
				"focus": 0.4339998999365399
			},
			"endBinding": {
				"elementId": "jMPPE6UiYmPHWsmZs3vf1",
				"gap": 1,
				"focus": 0.38775440647699455
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
					139.3365401952248,
					195.645053285788
				]
			]
		},
		{
			"type": "arrow",
			"version": 2842,
			"versionNonce": 1228721617,
			"isDeleted": false,
			"id": "RjWV_-7dlu_WpW-BuGxju",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 558.9823363847149,
			"y": -199,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 139.84020842344069,
			"height": 137.4129980899957,
			"seed": 1994013097,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1649230300680,
			"link": null,
			"startBinding": {
				"elementId": "QHz-osZXMgu933ItHP8KV",
				"gap": 1,
				"focus": -0.22918361778489546
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
					-139.84020842344069,
					137.4129980899957
				]
			]
		},
		{
			"type": "arrow",
			"version": 1078,
			"versionNonce": 1003533425,
			"isDeleted": false,
			"id": "tTJa2-suRJeVsGafeV_Yx",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 199.03326675908033,
			"y": -289.0661536514668,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 5.600705747535585,
			"height": 98.5291005291005,
			"seed": 641618279,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1649230300676,
			"link": null,
			"startBinding": {
				"elementId": "KxNbQ5mf4CbKZHRsjP73A",
				"gap": 5.767195767195801,
				"focus": -0.09284860897764113
			},
			"endBinding": {
				"elementId": "PLEDi3Rg",
				"gap": 1.7037037037036953,
				"focus": -0.018286501180512164
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
					5.600705747535585,
					-98.5291005291005
				]
			]
		},
		{
			"type": "arrow",
			"version": 1764,
			"versionNonce": 270300095,
			"isDeleted": false,
			"id": "t2GNyo4j",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 592.503306050609,
			"y": -261.9148936170213,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 4.389358474391372,
			"height": 61.56943277721285,
			"seed": 761105543,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1649230300680,
			"link": "",
			"startBinding": {
				"elementId": "QHz-osZXMgu933ItHP8KV",
				"gap": 1.9148936170212767,
				"focus": 0.2896377754097156
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
					4.389358474391372,
					-61.56943277721285
				]
			]
		},
		{
			"type": "ellipse",
			"version": 617,
			"versionNonce": 285724767,
			"isDeleted": false,
			"id": "RRZVOgJ_Wnk7KMJjFnreQ",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 916.3533834586469,
			"y": -71.97189021509791,
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
			"updated": 1649216741046,
			"link": null
		},
		{
			"type": "text",
			"version": 615,
			"versionNonce": 1863677617,
			"isDeleted": false,
			"id": "2upcqXHR",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 921.3533834586469,
			"y": -23.471890215097915,
			"strokeColor": "#0b7285",
			"backgroundColor": "transparent",
			"width": 259,
			"height": 37,
			"seed": 1225348327,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1649226326886,
			"link": null,
			"fontSize": 28,
			"fontFamily": 4,
			"text": "zotero Local in slave PC",
			"rawText": "zotero Local in slave PC",
			"baseline": 30,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "RRZVOgJ_Wnk7KMJjFnreQ",
			"originalText": "zotero Local in slave PC"
		},
		{
			"type": "rectangle",
			"version": 339,
			"versionNonce": 1979522993,
			"isDeleted": false,
			"id": "3nwrOLdu_jOB5rwShHhIF",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "dashed",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 780,
			"y": -263.298957884271,
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
			"updated": 1649216711447,
			"link": null
		},
		{
			"type": "text",
			"version": 339,
			"versionNonce": 742014577,
			"isDeleted": false,
			"id": "QrWrKQ1Q",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 785,
			"y": -241.29895788427098,
			"strokeColor": "#5c940d",
			"backgroundColor": "transparent",
			"width": 116,
			"height": 37,
			"seed": 2075318057,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1649226326887,
			"link": null,
			"fontSize": 28,
			"fontFamily": 4,
			"text": "data",
			"rawText": "data",
			"baseline": 30,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "3nwrOLdu_jOB5rwShHhIF",
			"originalText": "data"
		},
		{
			"type": "arrow",
			"version": 1405,
			"versionNonce": 1954138975,
			"isDeleted": false,
			"id": "ystW1pN8s8RXJ4OMFWU-s",
			"fillStyle": "solid",
			"strokeWidth": 4,
			"strokeStyle": "dotted",
			"roughness": 0,
			"opacity": 100,
			"angle": 0,
			"x": 238.7766742724761,
			"y": -428.6853822785898,
			"strokeColor": "#2b8a3e",
			"backgroundColor": "#ced4da",
			"width": 630.9512859118759,
			"height": 229.10907247794395,
			"seed": 773700489,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1649230300679,
			"link": null,
			"startBinding": {
				"elementId": "PLEDi3Rg",
				"focus": -0.5596716663215605,
				"gap": 5.386424394318851
			},
			"endBinding": {
				"elementId": "3nwrOLdu_jOB5rwShHhIF",
				"gap": 14.524970963994747,
				"focus": 0.8258059462031352
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
					630.9512859118759,
					150.8614534303241
				]
			]
		},
		{
			"type": "rectangle",
			"version": 498,
			"versionNonce": 997819423,
			"isDeleted": false,
			"id": "I95dVE-U4R7kPRvnIlC2w",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 1080,
			"y": -303.298957884271,
			"strokeColor": "#5f3dc4",
			"backgroundColor": "transparent",
			"width": 201,
			"height": 47,
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
			"updated": 1649216711447,
			"link": null
		},
		{
			"type": "text",
			"version": 501,
			"versionNonce": 1833492799,
			"isDeleted": false,
			"id": "k1ZXlaVj",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 1085,
			"y": -298.298957884271,
			"strokeColor": "#5f3dc4",
			"backgroundColor": "transparent",
			"width": 191,
			"height": 37,
			"seed": 786613097,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1649226326888,
			"link": null,
			"fontSize": 28,
			"fontFamily": 4,
			"text": "attachments",
			"rawText": "attachments",
			"baseline": 30,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "I95dVE-U4R7kPRvnIlC2w",
			"originalText": "attachments"
		},
		{
			"type": "arrow",
			"version": 1896,
			"versionNonce": 375820191,
			"isDeleted": false,
			"id": "79TKN4XVyoQ61k0RFUVKu",
			"fillStyle": "solid",
			"strokeWidth": 4,
			"strokeStyle": "dotted",
			"roughness": 0,
			"opacity": 100,
			"angle": 0,
			"x": 620,
			"y": -409.6254117370288,
			"strokeColor": "#5f3dc4",
			"backgroundColor": "#ced4da",
			"width": 443.1863860043162,
			"height": 130.3745882629712,
			"seed": 820300969,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1649216711517,
			"link": null,
			"startBinding": {
				"elementId": "YE0vFE1f",
				"focus": -0.1270141021482166,
				"gap": 6.3264538527578225
			},
			"endBinding": {
				"elementId": "A8yHYGqO",
				"focus": 0.268126753874877,
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
			"version": 843,
			"versionNonce": 938749087,
			"isDeleted": false,
			"id": "QEWdUolXPJz7VzC5Gbomc",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 1280,
			"y": -280,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 420.5830837361316,
			"height": 380.7010421157289,
			"seed": 802067847,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1649226397186,
			"link": null,
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
					420.5830837361316,
					380.7010421157289
				]
			]
		},
		{
			"type": "arrow",
			"version": 762,
			"versionNonce": 50792319,
			"isDeleted": false,
			"id": "iT3mOvaLO9KOATLmvjN0T",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 1.7832800941364138,
			"x": 961.6611817222324,
			"y": -163.06328575320114,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 81.62221175511263,
			"height": 99.34663239169217,
			"seed": 949279817,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1649230300679,
			"link": null,
			"startBinding": {
				"elementId": "RRZVOgJ_Wnk7KMJjFnreQ",
				"gap": 7.927319024791075,
				"focus": -0.30374516446136496
			},
			"endBinding": {
				"elementId": "3nwrOLdu_jOB5rwShHhIF",
				"gap": 18.54018987323707,
				"focus": 0.09384474657516129
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
					-81.62221175511263,
					99.34663239169217
				]
			]
		},
		{
			"type": "text",
			"version": 211,
			"versionNonce": 1616160255,
			"isDeleted": false,
			"id": "i73QmlQu",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "dotted",
			"roughness": 2,
			"opacity": 100,
			"angle": 0,
			"x": 480,
			"y": -180,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 263,
			"height": 38,
			"seed": 1374181806,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1649226332327,
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
			"version": 103,
			"versionNonce": 639100657,
			"isDeleted": false,
			"id": "A8yHYGqO",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 980,
			"y": -423.298957884271,
			"strokeColor": "#5f3dc4",
			"backgroundColor": "transparent",
			"width": 153,
			"height": 33,
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
			"updated": 1649216711447,
			"link": null,
			"fontSize": 27.317073170731707,
			"fontFamily": 4,
			"text": "OneDrive slave",
			"rawText": "OneDrive slave",
			"baseline": 27,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "OneDrive slave"
		},
		{
			"type": "arrow",
			"version": 519,
			"versionNonce": 551010207,
			"isDeleted": false,
			"id": "vjrAPyWehObaqWzKXU-cb",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 1069.0559083757921,
			"y": -388.9575750823243,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 87.69632805310948,
			"height": 79.2305532349393,
			"seed": 395464370,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1649230300679,
			"link": null,
			"startBinding": {
				"elementId": "A8yHYGqO",
				"gap": 1.3413828019466791,
				"focus": 0.07590714376734986
			},
			"endBinding": {
				"elementId": "I95dVE-U4R7kPRvnIlC2w",
				"gap": 6.4280639631140275,
				"focus": 0.07412882986585002
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
					87.69632805310948,
					79.2305532349393
				]
			]
		},
		{
			"type": "text",
			"version": 265,
			"versionNonce": 1437672767,
			"isDeleted": false,
			"id": "d7sckug2",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 1160,
			"y": -360,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 348,
			"height": 38,
			"seed": 1603283570,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1649226386781,
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
			"version": 283,
			"versionNonce": 304722129,
			"isDeleted": false,
			"id": "Rzx7vmWl",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "dotted",
			"roughness": 2,
			"opacity": 100,
			"angle": 0,
			"x": 1100,
			"y": -240,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 263,
			"height": 38,
			"seed": 2024736302,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1649226397186,
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
			"version": 73,
			"versionNonce": 1325912383,
			"isDeleted": false,
			"id": "1RPfolFw",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 900,
			"y": 116.70104211572902,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 423.3473980309423,
			"height": 500,
			"seed": 51192,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1649216758273,
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
			"version": 65,
			"versionNonce": 1078486641,
			"isDeleted": false,
			"id": "zxhsYtvj",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 1440,
			"y": 96.70104211572902,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 427.9210925644917,
			"height": 500.00000000000006,
			"seed": 36958,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1649216711447,
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
			"version": 505,
			"versionNonce": 1363025201,
			"isDeleted": false,
			"id": "QHz-osZXMgu933ItHP8KV",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 460,
			"y": -260,
			"strokeColor": "#5f3dc4",
			"backgroundColor": "transparent",
			"width": 201,
			"height": 60,
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
				},
				{
					"id": "t2GNyo4j",
					"type": "arrow"
				}
			],
			"updated": 1649226270952,
			"link": null
		},
		{
			"type": "text",
			"version": 506,
			"versionNonce": 1979721745,
			"isDeleted": false,
			"id": "03hIh8kH",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 465,
			"y": -248.5,
			"strokeColor": "#5f3dc4",
			"backgroundColor": "transparent",
			"width": 191,
			"height": 37,
			"seed": 1843416945,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1649226326889,
			"link": null,
			"fontSize": 28,
			"fontFamily": 4,
			"text": "attachments",
			"rawText": "attachments",
			"baseline": 30,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "QHz-osZXMgu933ItHP8KV",
			"originalText": "attachments"
		}
	],
	"appState": {
		"theme": "light",
		"viewBackgroundColor": "#ffffff",
		"currentItemStrokeColor": "#5c940d",
		"currentItemBackgroundColor": "transparent",
		"currentItemFillStyle": "hachure",
		"currentItemStrokeWidth": 2,
		"currentItemStrokeStyle": "solid",
		"currentItemRoughness": 1,
		"currentItemOpacity": 100,
		"currentItemFontFamily": 4,
		"currentItemFontSize": 36,
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