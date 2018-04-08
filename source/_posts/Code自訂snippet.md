---
title: Code自訂snippet
comments: true
date: 2018-04-08 21:18:35
tags:
categories:
---


最近寫blog很長手動打`<!--more-->`
想說snippet來增加寫文章速度
也不用在找`asset_img`
語法我真的記不太住....

<!--more-->

目前我為Hexo設定的snippets
```json
{
	"Read More": {
  		"prefix": "!more",
  		"body": [
    			"<!--more-->"
  		],
  		"description": "Read More"
	},
	"asset_img": {
		"prefix": "asset",
    	"body": [
    		"{% asset_img ${1:img.png} ${1:title} %}"
  		],
  		"description": "asset_img"
	},
	"asset_link": {
		"prefix": "asset",
		"body": [
		"{% asset_link ${1:url} ${1:title} %}"
		],
		"description": "asset_link"
	}
}
```

這三個寫文章應該很夠用



##參考來源
[snippet generator](https://pawelgrzybek.github.io/snippet-generator/?description=asset_link&tabtrigger=asset_link&snippet={%25+asset_link+%24{1%3Aurl}+%24{2%3Atitle}+%25}&mode=vscode)