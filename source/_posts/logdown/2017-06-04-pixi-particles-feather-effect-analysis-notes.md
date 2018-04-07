---
layout: post
title: 'pixi-particles 羽毛特效分析筆記'
date: 2017-06-04 13:24
comments: true
categories: [pixi]
---
![](http://i.imgur.com/gSfaqHF.gif)
上篇記錄了[[CSS]看的到確摸不到的pointer-events屬性]( http://malagege.logdown.com/posts/1864913-css-does-not-touch-the-pointer-events-property)
裡面還有很炫的旋轉嶼毛特效

本來以為是計時器慢慢一秒做下去做旋轉
結果看code，竟然是pixijs特效做的
<!--more-->

[突破 D3.js 的速度極限 — 2D WebGL 與 PIXI.js](http://blog.infographics.tw/2015/12/pixi-introduction/)
[Pixi.js 中文教學](https://www.gitbook.com/book/supermrji/pixi-js/details)

簡單爬一下，PixiJS算是能幫你做出動畫用WebGL 加速
因此動畫會非常順暢(應該吧，我沒用過)

旋轉是透過下面的套件來實作的
[pixijs/pixi-particles: A particle system for PixiJS](https://github.com/pixijs/pixi-particles)
官網有一些範例

雨毛旋轉範例是用snow特效
官方雨毛旋轉特效
```json
{
	"alpha": {
		"start": 1,
		"end": 0.5
	},
	"scale": {
		"start": 1,
		"end": 0.7,
		"minimumScaleMultiplier": 0.5
	},
	"color": {
		"start": "#ffffff",
		"end": "#ffffff"
	},
	"speed": {
		"start": 100,
		"end": 75
	},
	"acceleration": {
		"x": 0,
		"y": 0
	},
	"startRotation": {
		"min": 80,
		"max": 100
	},
	"rotationSpeed": {
		"min": 10,
		"max": 40
	},
	"lifetime": {
		"min": 10,
		"max": 10
	},
	"blendMode": "normal",
	"ease": [
		{
			"s": 0,
			"cp": 0.379,
			"e": 0.548
		},
		{
			"s": 0.548,
			"cp": 0.717,
			"e": 0.676
		},
		{
			"s": 0.676,
			"cp": 0.635,
			"e": 1
		}
	],
	"frequency": 0.5,
	"emitterLifetime": -1,
	"maxParticles": 140,
	"pos": {
		"x": 0,
		"y": 0
	},
	"addAtBack": false,
	"spawnType": "rect",
	"spawnRect": {
		"x": 0,
		"y": 0,
		"w": 900,
		"h": 100
	}
}
```
可把上方內容載入到[PixiParticlesEditor](http://pixijs.github.io/pixi-particles-editor/)
在把雨毛傳到上面，這樣就可以在PixiParticlesEditor看到特效

pixi-particles上面還有很多特效值的玩一玩
好像還能運用在RPG Maker上面
[PixiParticlesEditor　pixi.js特效 - RPG Maker MV 讨论区 - 66RPG - Powered by Discuz!](http://rm.66rpg.com/thread-386105-1-1.html)
參考來源：
[pixi.jsを勉強する[2] -PIXI Particles- - [ALL]| アクアリングBLOG](http://blog.aquaring.co.jp/517)
[PixiParticlesEditor](http://pixijs.github.io/pixi-particles-editor/)
