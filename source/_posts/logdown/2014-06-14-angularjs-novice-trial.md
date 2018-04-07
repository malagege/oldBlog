---
layout: post
title: 'AngularJS新手小試'
date: 2014-06-14 06:46
comments: true
categories: AngularJS
tags: AngularJS
---
AngularJS是一個MVC架構JavaScript Framework，是這幾年比較紅的。[詳細介紹維基](http://zh.wikipedia.org/wiki/AngularJS)

最近簡單做了簡單輸入檔案計算，如下
<!--more-->


```html
<!doctype html>
<html lang="zh-TW" ng-app>
<head>
	<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.3.0-beta.11/angular.min.js"></script>
	<meta charset="UTF-8">
	<title>簡易檔案計算程式  WEB</title>
	<script>
	function select_all(){
	 var textBox = document.getElementById("foo");

	textBox.focus();
	textBox.select();
	}
	showFileData =function ($scope){
		$scope.test = function(){
			if ($scope.isShowGB) {
				if ($scope.fileGBMB=="GB") {
					$scope.fileData = $scope.fileNumber+"GB";
				}else{
					$scope.fileData = Number($scope.fileNumber/1024).toFixed(1)+"GB";
				}
			}else{
				if ($scope.fileGBMB=="GB") {
					$scope.fileData = Number($scope.fileNumber*1024).toFixed(0)+"MB";
				}else{
					$scope.fileData = $scope.fileNumber+"MB";
				}
			}
		}

	}

	</script>
</head>
<body style="margin: 10px auto; width:90%;line-height:36px;background-color:#e6e6e6" ng-controller="showFileData" ng-form="form1">
	<input type="checkbox" ng-model="isShowGB"  ng-change="test()"  />顯示GB<br />
	檔案大小：<input  type="number" ng-model="fileNumber"  ng-change="test()" required /><select ng-model="fileGBMB" ng-init="fileGBMB='GB'" ng-change="test()" required>
		<option value="GB">GB</option>
		<option value="MB">MB</option>
	</select><br />
<div ng-hide="form1.$invalid">{{fileData}}</div>
<div ng-show="form1.$invalid"><h1>必要欄位沒填!!!</h1></div>
</body>
</html>
```

本實驗只有用到
* ng-model
* ng-controller
* ng-click
* ng-init
* ng-change
* ng-form
* ng-show / ng-hide

這次表單驗證是利用AngularJS內鍵功能來實作，`form1.$invalid`會傳會現在欄位是否真入正確值，還會查看HTML`type`是否符合型態，ng-show

透過這次簡單的實驗發現AngularJS是一個很實用的工具，不知道是不是JQuery Selector用習慣的係，有時候控制裡面的原件都不知道如何去控制ng-show/ng-hide來顯示出結果要呈現。

Google搜尋到不錯30天學AngularJS教學
http://ithelp.ithome.com.tw/question/10132196