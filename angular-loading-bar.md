# angular-loading-bar

## 背景
requirejs+Angularjs

## 实现记录：

1. Requirejs中增加路径及依赖

```javascript
require.config({
    baseUrl: './',
    paths: {
        'jquery':'components/jquery/dist/jquery.min',       
        'angular': 'components/angular/angular.min',  
        'angular-loading-bar':'components/angular-loading-bar/loading-bar.min' 
    },
    deps: ['jquery'],
    shim: {
        'angular': {exports: 'angular',deps: ['jquery']},
        'angular-loading-bar': {deps: ['angular']}     
    }
});
```
2. 在module中注册

```javascript
var myApp = angular.module('myApp', ['angular-loading-bar']);
myApp .config(['cfpLoadingBarProvider', function(cfpLoadingBarProvider){
    //cfpLoadingBarProvider.includeSpinner = false;  
    // 自定义template，增加了个背景遮罩
	cfpLoadingBarProvider.spinnerTemplate = ' <div class="loading-backdrop fade"></div><div id="loading-bar-spinner"><div class="spinner-icon"></div></div>';
 }]);
```

3. CSS样式

```css
//加载条样式
#loading-bar .bar {
    background: #00C0EF;     
}

#loading-bar .peg {
    -moz-box-shadow: #00C0EF 1px 0 6px 1px;
    -ms-box-shadow: #00C0EF 1px 0 6px 1px;
    -webkit-box-shadow: #00C0EF 1px 0 6px 1px;
    box-shadow: #00C0EF 1px 0 6px 1px;
}
//旋转小圆圈位置
#loading-bar-spinner {
    top: 50%;
    left: 50%;    
}
//旋转小圆圈的大小和颜色
#loading-bar-spinner .spinner-icon {
    width: 35px;
    height: 35px;
    border-top-color: #00C0EF;
    border-left-color: #00C0EF;
}

.loading-backdrop{
    position: fixed;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    z-index: 1051;
    background-color: #000;
    opacity: .5
}
```
4. 特别功能：
实现对某个请求屏蔽loading-bar

**注意: $resoure中的名字`get`要和`resultAarry.get`名字一致！**

```javascript
getXHR : function() {
  var defer = $q.defer(); 
  var responseResult  = $resource('aMoudule/getRequest',{},{
	  get:{
		  ignoreLoadingBar:true
	  }
  });
	  responseResult.get(function (res) {
		  defer.resolve(res);
	  },function (res) {    			  
		  defer.reject(res);
	  })
return defer.promise;    		  
},
```

## 扩展
https://github.com/chieffancypants/angular-loading-bar

###下载:
$ npm install angular-loading-bar
$ bower install angular-loading-bar


### 基本语法：
- spinner旋转的小圆圈开启或关闭(默认开启)

```javascript
angular.module('myApp', ['angular-loading-bar'])
	.config(['cfpLoadingBarProvider', function(cfpLoadingBarProvider) {
		cfpLoadingBarProvider.includeSpinner = false;
}])
```
- loading bar 开启或者关闭(默认开启)

```javascript
angular.module('myApp', ['angular-loading-bar'])
  .config(['cfpLoadingBarProvider', function(cfpLoadingBarProvider) {
		cfpLoadingBarProvider.includeBar = false;
  }])
```
- 自定义加载模板 

```javascript
angular.module('myApp', ['angular-loading-bar'])
.config(['cfpLoadingBarProvider', function(cfpLoadingBarProvider) {
    cfpLoadingBarProvider.spinnerTemplate = '<div><span class="fa fa-spinner">Loading...</div>';
}])
```
- 延迟出现加载条(默认收到response的100ms内出现)

```javascript
angular.module('myApp', ['angular-loading-bar'])
  .config(['cfpLoadingBarProvider', function(cfpLoadingBarProvider) {
    cfpLoadingBarProvider.latencyThreshold = 500;
  }])
```
- 某些XHR请求屏蔽加载条出现

```javascript
 // ignore a particular $http GET:
$http.get('/status', {
  ignoreLoadingBar: true
});

// ignore a particular $http POST.  Note: POST and GET have different
// method signatures:
$http.post('/save', data, {
  ignoreLoadingBar: true
});
```
```javascript
// ignore particular $resource requests:
.factory('Restaurant', function($resource) {
  return $resource('/api/restaurant/:id', {id: '@id'}, {
    query: {
      method: 'GET',
      isArray: true,
      ignoreLoadingBar: true
    }
  });
});
```

