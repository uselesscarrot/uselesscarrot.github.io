---
title: jQuery Plugin 만드는 법
updated: 2016-08-22
---

# How to Create a Basic Plugin

> 본 문서는 http://learn.jquery.com/plugins/basic-plugin-creation/ 를 번역한 문서입니다.

때때로 당신의 코드 전반에서 쓰일 수 있는 기능의 조각을 만들고 싶을겁니다. 예를 들어, 당신이 호출한 jQuery 선택자에 일련하여 동작하는 메소드를 만들고 싶을 때 그럴 때 플러그인을 만들면 좋습니다.

## How jQuery Works 101: jQuery Object Methods
우리가 우리만의 플러그인을 만들기에 앞서, 어떻게 jQuery가 작동하는지 알아봅시다. 아래 코드를 보세요.

```
$("a").css("color","red");
```

아주 기본적인 jQuery 코드 입니다. 하지만 뒤에서 어떻게 돌아가고 있는지 알고 있나요? ```$``` 함수는 요소를 선택할 때 쓰고 jQuery 객체를 반환합니다. 이 객체는 (```.css()```, ```.click()```, 등.)과 같은 메소들를 포함합니다. 그리고 모든 요소들은 당신의 선택자에 맞게 되는 것이죠. jQuery 객체는 ```$.fn``` 객체로붜 이런 메소드들을 받습니다. 이 객체는 모든 jQuery 객체 메소드를 포함하고 있으니, 우리만의 메소드를 만들고 싶다면 이런 메소드들을 잘 활용할 필요가 있겠죠?

## Basic Plugin Authoring

텍스트를 초록색으로 바꾸는 플러그인을 만든다고 가정해봅시다. ```greenify```를 ```$.fn```로 호출하는 함수를 추가합니다. 그러면 다른 jQuery 객체 메소드와 마찬가지로 사용이 가능합니다.

```
$.fn.greenify = function() {
this.css("color", "green");
};

$("a").greenify();
```

```.css()```나 다른 메소드를 쓸 때 ```this```를 쓰지 ```$(this)```를 쓰지 않는다는 점에 유의하세요. ```greenify```함수는 ```.css()```와 같은 부분에 있는 객체이기 때문입니다.

## Chaining

잘 되나요? 하지만 실제로 쓰기 위해선 몇가지 일을 더 해줘야 쓸만해질겁니다. jQuery의 특징중 하나인 chaining이 그렇습니다. 하나의 선택자에 5~6개의 액션을 엮는 일입니다. 모든 jQuery 객체 메소드가 원래 jQuery 객체를 다시 반환함으로서 이뤄집니다. (몇가지 예외도 있습니다. ```.width()```는 파라미터 없이 선택된 요소의 너비를 반환하며 chaining되지 않습니다.) 우리의 플러그인 메소드를 chainable하게 만드는 코드는 아래와 같습니다.

```
$.fn.greenify = function(){
this.css("color", "green");
return this;
}
$("a").greenify().addClass("greenified");
```

## Protecting the $ Alias and Adding Scope

```$``` 변수는 JavaScript 라이브러리에서 굉장히 광범위하게 쓰입니다. jQuery와 다른 라이브리러를 같이 쓴다면, jQuery에서 ```$```를 쓰지 못하도록 ```jQuery.noConflict()```를 써주어야 합니다. 하지만 이럴 경우 ```$```는 ```jQuery``` 함수의 별칭이라는 가정으로 플러그인을 만들고 있으므로 우리 코드를 망가뜨리게 될겁니다. 다른 플러그인들과 잘 작동하게 하기 위해, 그리고 jQuery를 ```$```기호와 계속 쓰기 위해 우리는 우리 코드 안에 [IIFE(Immediately-Invoked Function Expression)([http://benalman.com/news/2010/11/immediately-invoked-function-expression/)를 넣어 ```$```를 파라미터명으로 정하고 ```jQuery``` 함수에 넘겨야 합니다.

```
(function ($){
$.fn.greenify = function(){
this.css("color", "grren");
return this;
};
}(jquery));
```

사실 IIF의 주된 목적은 우리만의 프라이빗 변수를 가질 수 있게 하는 것입니다. 초록색 외의 색을 원한다고 가정하고 변수에 넣어봅시다.

```
(function ($){
var shade = "#556b2f";
$.fn.greenify = function() {
this.css("color", shade);
return this;
};
}(jquery));
```

## Minimizing Plugin Footprint

```$.fn```에서 하나의 슬롯만을 차지하여 플러그인을 만드는 것은 매우 좋은 습관입니다. 여러분의 플러그인이 오버라이드 될 확률과, 다른 플러그인을 오버라이드할 확률을 줄여주기 때문이죠. 다시 말하면 이 경우는 매우 안 좋은 경우라고 할 수 있습니다.

```
(function ($) {

$.fn.openPopup = function() {
// Open popup code.
};

$.fn.closePopup = function() {
// Close popup code.
};

}(jQuery));
```


하나의 슬롯으로 진행하는 것이 훨씬 낫습니다. 파라미터를 이용하여 하나의 슬롯이 액션을 수행하도록 만듭시다.

```
(function($){
$.fn.popup = function(action){
if (action === "open") {
// open popup code.
}
if(action === "close") {
// close popup code.
}
};
}(jquery));
```

## Using the each() Method

jQuery 객체는 보통 DOM 요소의 수에 대한 참조를 포함합니다. 그래서 jQuery 객체를 컬렉션이라고 불리는 것이죠. 특정 요소에 어떤 조작을 수행(예를 들어 데이터 속성을 받는다거나, 특정 위치를 계산하는 작업)하려고 한다면 ```.each()```를 이용하여 해당 요소에 루프를 돌려야 합니다.

```
$.fn.myNewPlugin = function() {
return this.each(function(){
// Do something to each element here.
});
};
```

```this```를 반환하는 것 대신 ```.each()```의 결과를 반환하게 된다는 것을 유념하세요. ```.each()```는 이미 chainable하기 때문에, 우리가 반환할 값 this를 반환합니다. 우리가 지금까지 해왔던 방식보다 chainablity를 유지하는 것이 더 좋은 방법입니다.

## Accepting Options

당신의 플러그인이 더욱더 복잡해진다면, 옵션들을 통해 커스터마이징 가능하게 만드는 것도 좋은 아이디어입니다. 이런 일을 하기 위한 가장 쉬운 방법은 객체 리터럴을 이용하는 것입니다. 우리의 greenify 플러그인을 몇가지 옵션을 받을 수 있도록 바꿔봅시다.

```

(function ( $ ) {
 
    $.fn.greenify = function( options ) {
 
        // This is the easiest way to have default options.
        var settings = $.extend({
            // These are the defaults.
            color: "#556b2f",
            backgroundColor: "white"
        }, options );
 
        // Greenify the collection based on the settings variable.
        return this.css({
            color: settings.color,
            backgroundColor: settings.backgroundColor
        });
 
    };
 
}( jQuery ));
```

용법 예제:

```
$( "div" ).greenify({
    color: "orange"
});
```

```color```의 기본 값은 ```#556b2f```이지만, ```$.extend()```로 오버라이드 되어 오렌지로 바뀌었습니다.

## Putting It Together

여기 우리가 이야기 했던 몇가지 기술들을 이용하여 만든 플러그인 예제가 있습니다.

```
(function( $ ) {
 
    $.fn.showLinkLocation = function() {
 
        this.filter( "a" ).each(function() {
            var link = $( this );
            link.append( " (" + link.attr( "href" ) + ")" );
        });
 
        return this;
 
    };
 
}( jQuery ));
 
// Usage example:
$( "a" ).showLinkLocation();
```

이 간단한 플러그인은 컬렉션의 모든 앵커에 적용 되고,  ```href``` 속성을 괄호 안에 추가 합니다.

```
<!-- Before plugin is called: -->
<a href="page.html">Foo</a>
 
<!-- After plugin is called: -->
<a href="page.html">Foo (page.html)</a>
```

이런 식으로 최적화 될 수 있겠죠.

```
(function( $ ) {
 
    $.fn.showLinkLocation = function() {
 
        this.filter( "a" ).append(function() {
            return " (" + this.href + ")";
        });
 
        return this;
 
    };
 
}( jQuery ));
```

```.append()``` 메소드를 이용하여 콜백을 허용하고, 콜백의 반환값은 컬렉션의 각 요소에 추가 됩니다. 기본 DOM API가 href 속성으로 쉽게 접근 할 수 있도록 되어있기 때문에 ```.attr()```메소드로 ```href```속성을 검색하는 방법을 사용하지 않았습니다. 
