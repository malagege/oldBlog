---
layout: post
title: 'AngularJS讀取載入檔案資訊'
date: 2014-07-03 19:26
comments: true
categories: AngularJS
---
http://www.dotblogs.com.tw/regionbbs/archive/2012/06/28/html5.file.api.aspx
http://odetocode.com/blogs/scott/archive/2013/07/05/a-file-input-directive-for-angularjs.aspx
http://stackoverflow.com/questions/16579427/html5-file-upload-with-angularjs
http://plnkr.co/edit/JPxSCyrxosVXfZnzEIuS?p=preview
http://plnkr.co/edit/HEA7wYq8Uv0Ee0J4SX7u?p=preview

html
```html
<!doctype html>
<html ng-app="plunker" >
<head>
  <meta charset="utf-8">
  <title>AngularJS Plunker</title>
  <script>document.write('<base href="' + document.location + '" />');</script>
  <link rel="stylesheet" href="style.css">
  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.0.6/angular.js"></script>
  <script src="app.js"></script>
</head>
<body ng-controller="MainCtrl">
<input type="file" filelist-bind multiple="multiple"  name="files"/>
<br>
files : <pre>{{ files | json }}</pre>
</body>
</html>

```

```javascript
var app = angular.module('plunker', []);

app.directive('filelistBind', function() {
  return function( scope, elm, attrs ) {
    elm.bind('change', function( evt ) {
      scope.$apply(function() {
        name ="";
        for(i=0;i<evt.target.files.length;i++){
          alert(evt.target.files[i].name);
          name += evt.target.files[i].name;
        }
        scope[ attrs.name ] = name;
      });
    });
  };
});

app.controller('MainCtrl', function($scope) {
  $scope.name = 'World';
});

```


http://stackoverflow.com/questions/16207202/required-attribute-not-working-with-file-input-in-angular-js
http://jsfiddle.net/danielzen/utp7j/
