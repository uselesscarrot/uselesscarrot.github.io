---
title: 첫 개인 과제 (2)
updated: 2016-02-24
---

지난 편에선 api를 이용하여 제가 원하는 프로그램 구현을 위해서 어떤 값들을 불러오면 되는지에 대한 정보만을 확인했습니다. 이번 편에는 본격적으로 JSON 데이터를 어떻게 처리했는지 정리해보려고 합니다.

### Riot Games Developer Page을 통해 구한 데이터들

1. [Riot Games Developer Page](https://developer.riotgames.com/) 내 API Reference   
2. champion-v1.2 를 통한 현재 Free to Play가 가능한 챔피언들의 id 리스트 : https://kr.api.pvp.net/api/lol/kr/v1.2/champion?freeToPlay=true&api_key=API키값   
3. 2에서 얻은 id를 이용하여 static-data-v1.2에서 얻을 수 있는 챔피언 정보 : https://global.api.pvp.net/api/lol/static-data/kr/v1.2/champion/{ID값}?api_key=API키값   
4. 3에서 얻은 챔피언명을 이용하여 얻을 수 있는 League of Legends가 제공하는 챔피언 이미지 : http://ddragon.leagueoflegends.com/cdn/img/champion/splash/챔피언명_0.jpg

---

### AngularJS를 이용하고 싶었어요

사실 javascript에 대한 정확한 개념도 서지 않고 실력도 갖춰지지 않은 상태에서 jQuery로 점프! 그러다 또 다시 jQuery를 대충 겉핥고 AngularJS를 단기간 공부해서 MVC패턴대로 체계적으로 코드를 짜기란 쉬운 일이 아니었습니다. 제가 다 게으른 탓입니다. 흡... 그리고 제 이해력도 한몫할 것 같아요.
어찌되었든 전 최종적으로 AngularJS를 공부했기 때문에 AngularJS로 구현을 하고 싶었습니다. 하지만 JSON 데이터를 비동기로 처리하고 이걸 위해선 Service도 써야하고, 하지만 전 하나의 Controller에 다 때려박고 있었고 그 마저도 제대로 동작하지 않았고... JSONP라는 애도 처음 봤고... 혼란함으로 이틀을 보낸 것 같아요. javascript에 대한 더 깊은 이해와 뛰어난?(이러면 평생 못할수도 있겠네요. 흠흠;) jQuery 활용력을 갖춰졌을 때즈음 AngularJS로 다시 재도전 해봐야겠어요.

{% highlight js %}
var slides = $scope.slides = [];
var champNames = $scope.champNames = [];
var champKeys = $scope.champKeys = [];

$http.get('https://kr.api.pvp.net/api/lol/kr/v1.2/champion?freeToPlay=true&api_key=API키값').
        success(function(data){
            $scope.champions = data.champions;
            for(var i = 0; i < data.champions.length; i++) {
    $http.get('https://global.api.pvp.net/api/lol/static-data/kr/v1.2/champion/'+ $scope.champions[i].id +'?api_key=API키값').
        success(function(idforname){
            $scope.champNames[i] = idforname.name;
            $scope.champKeys[i] = idforname.key;
            console.log(champNames[i]+":"+i+":"+champKeys[i]);
            $scope.slides.push("{name: '"+idforname.name+"', image: 'http://ddragon.leagueoflegends.com/cdn/img/champion/splash/"+idforname.key+"_0.jpg'}");
        });
    }
});
{% endhighlight %}

AngularJS로 짜려다 그냥 할 수 있는(구글링 할 수 있는) 모든 방법을 동원해서 대충 짠 코드입니다. 그래보이죠?   
먼저 변수 선언을 한 ``` var slides = $scope.slides = []; ``` 와 같은 부분은 제가 구글링 하다가 처음 봐서 신기해서 따라서 써봤는데 되긴 되더라고요. 어떻게 저렇게 쓸 수 있는건지도 잘 모르겠고 왜 굳이 저렇게 선언을 하는지도 잘 모르겠지만...   

```$http.get()```을 통해서 JSON을 받아 그대로 해당 배열의 길이만큼 for문을 돌려 하나하나 i값에 맞는 다른 JSON값을 부르길 바랐어요. 그래서 $http.get()메소드 안에 또 $http.get()메소드가 있네요. 코드가 더러운지 아닌지 판단을 할 수 없는 실력의 저지만 저 코드가 굉장히 불안해 보인다는건 왠지 모르게 제게도 느껴집니다.
또 다른 문제는 for문 내 변수 i였어요. i를 그대로 전달하면 내부에 있는 $http.get()가 받는 url에 알맞은 i값(제가 기대한 것은 0부터 9까지의 값이 차례대로 들어가는 것이었습니다.)이 들어가 ```$scope.champions[i].id```가 id별로 JSON 데이터를 하나씩 불러올 수 있을거라고 생각했거든요. i는 0부터 차례대로 i++ 된다고 써놨으니까요! 하지만 이 또한 제 뜻대로 되지 않았습니다. console.log로 확인해보니 $http.get내로 전달될 때 무조건 i값이 **10**으로만 전달되더라고요. 아무래도 JSON 내 JSON을 받는 것과 관련이 있는 것 같아 검색을 해보니 [How to retrieve multiple JSON objects with a for loop in javascript](http://stackoverflow.com/questions/16380714/how-to-retrieve-multiple-json-objects-with-a-for-loop-in-javascript), [WebNinja Tutorial: Asynchronous For Loops](http://blog.chaoscollective.org/post/40284901138/webninja-tutorial-asynchronous-for-loops)와 같은 글들이 있어 이를 참고하여 코드를 수정했습니다. 

{% highlight js %}
    for(var i = 0; i < data.champions.length; i++) {
            (function(i){
                $http.get('https://global.api.pvp.net/api/lol/static-data/kr/v1.2/champion/'+ data.champions[i].id +'?api_key=API키값').
                success(function(idforname){
                    $scope.champNames[i] = idforname.name;
                    $scope.champKeys[i] = idforname.key;
                    $scope.slides.push("{name: '"+idforname.name+"', image: 'http://ddragon.leagueoflegends.com/cdn/img/champion/splash/"+idforname.key+"_0.jpg'}");
                });
            })(i);
{% endhighlight %}

수정한 for문은 위와 같습니다.   
그러나 문제는 계속 되었어요. 저는 분명히 값을 하나하나 ```$scope.champNames[i] = idforname.name;```으로 배열에 잘 넣고 있다고 생각했거든요. 하지만 $http.get()메소드 바깥에서 console.log를 이용하여 배열을 찍어보면 배열이 항상 빈 배열([])로 나왔습니다. $http.get() 바깥에 전역변수로 선언한 배열인데 왜 $http.get()만 빠져나오면 빈 배열이 되어버리는걸까요? 자꾸 빈 배열을 뱉는걸 보니 모두 이 안에서 해결해야한다는 압박감에 코드를 짜면 짤수록 코드가 뚱뚱해져서 슬펐어요. 이때까지만 해도 왜 그런지 원인을 모른채 제대로 알지도 못하는 AngularJS를 버리자고 마음먹습니다. jQuery로 코드를 다시 짜게 됩니다. 뭐 어차피 $http.get()이냐 $.getJSON()이냐의 차이 뿐인 것 같아서 (그정도 차이만 나게끔 AngularJS를 쓰고 있었으므로(...)) 코드를 수정하는데에는 크게 어려움이 없었어요.   


### jQuery로 재도전하기

{% highlight js %}
$(document).ready(function(){
$.getJSON('https://kr.api.pvp.net/api/lol/kr/v1.2/champion?freeToPlay=true&api_key=API키값', function(data) {
        for (var i in data.champions) {
            (function(i){
                $.getJSON('https://global.api.pvp.net/api/lol/static-data/kr/v1.2/champion/'+ data.champions[i].id +'?api_key=API키값',
                function(id){
                    console.log(id.name+" : "+id.key+":"+i);
                    $(".champs:eq("+i+")").append('<img src='+'"http://ddragon.leagueoflegends.com/cdn/img/champion/splash/'+id.key+'_0.jpg">');
                    // $(".champs:eq("+i+")").attr('src', 'http://ddragon.leagueoflegends.com/cdn/img/champion/splash/'+id.key+'_1.jpg');
                    // $('.single-item').children('div').append('<img src='+'"http://ddragon.leagueoflegends.com/cdn/img/champion/splash/'+id.key+'_1.jpg">');
                }); //key값으로 이미지 받아오기
            })(i);
        } // for문 끝
    });
});
{% endhighlight %}

많은 시행착오를 겪고나서 나름대로 깔끔하게 정리하여 jQuery로 만든 코드입니다. $.getJSON을 중첩 시켜놨더니 해당 메소드 바깥으로 값을 넘길 수가 없게 되어서 계속 이 상태로 진행했습니다. 중첩된 코드들을 따로따로 분리해야하는 방법을 몰랐거든요. 어떤 것을 인자로 넘겨주고 정리해야할지에 대한 개념이 부족하다는 걸 절실히 느꼈습니다. 흡...   
위 AngularJS로 짠 마지막 코드에서 $http.get()으로 받은 데이터를 메소드 바깥에 어떻게 저장해야 하는 지에 대해선 [How to get variables value outside $http.get?](http://stackoverflow.com/questions/28169376/how-to-get-variables-value-outside-http-get), [How do I access json data outside of $.getJSON()?](https://forum.jquery.com/topic/how-do-i-access-json-data-outside-of-getjson) 등의 페이지를 통해 대답을 얻었습니다. 어려움을 겪을 때마다 가장 많이 본 단어는 결국 **Asynchronous**였어요. 비동기에 대한 이해가 없어서 문제가 계속 생겼던 것 같아요. 
   
데이터를 제대로 받아오니 나머지는 다 쉬워졌어요. ```<div class="champs">```를 10개 만들어 eq()를 이용하여 원하는 이미지를 담을 수 있게 처리하였습니다. slick이라는 jQuery Carousel Plugin을 이용하여 이미지들을 슬라이드 할 수 있게 했고요. 하지만 전 제 코드가 불안정해보여서 결국 팀장님께 도움을 요청하게 됩니다. 하핫? 알아서 다 완벽하게 만들어 가고 싶었는데! 실력이 완벽하지 않으니 별 수 없죠. 헤헤;


### 코드 정리

{% highlight js %}

function getCharacterInfo(char_id, index) {
    $.getJSON('https://global.api.pvp.net/api/lol/static-data/kr/v1.2/champion/'+ char_id +'?api_key=API키값',
    function(data){
        var dataKey = data.key;
        $(".champs:eq("+index+")").append('<img src='+'"http://ddragon.leagueoflegends.com/cdn/img/champion/splash/'+data.key+'_0.jpg">');
        getVersionInfo(dataKey);
    });
};

function getVersionInfo(data_key){
    $.getJSON('https://global.api.pvp.net/api/lol/static-data/kr/v1.2/versions?api_key=API키값', function(data){
        console.log(data[0]+": 이것은 데이터_키이다 -->"+data_key);
        //썸네일 이미지 -> "http://ddragon.leagueoflegends.com/cdn/"+data[0]+"/img/champion/"+data_key+".png"
    });
};

$(document).ready(function(){
    $.getJSON('https://kr.api.pvp.net/api/lol/kr/v1.2/champion?freeToPlay=true&api_key=API키값', function(data) {
        for (var i in data.champions) {
            var currentItem = data.champions[i];
            getCharacterInfo(currentItem.id, i);
        }
    });
});

{% endhighlight %}

사실 전 DOM을 이용 안 할건데도 전부 ```$(document).ready(function(){});```안에 내가 원하는 걸 전부 다 넣어야 한다는 생각을 하고 있었나봅니다. 팀장님이 제 코드를 보시더니 꼭 하나의 함수에 다 넣지 않고 빼도 된다고 말씀하시며 ```var currentItem = data.champions[i];```와 ```getCharcterInfo(currentItem.id, i);```를 정의해주셨어요. $.getJSON()이 중첩되지 않으니 for문 내 변수 i가 이상하게 전달되는 상황도 사라졌습니다. 파라미터로 뭘 써야하고 어떻게, 무슨 값을 전달해야하는 지에 대한 개념이 잡혀있지 않아서 어려웠는데 한번 예시를 보여주시니까 getVersionInfo()와 같은 함수도 만들 수 있게 되어 다른 기능도 넣을 수 있게 되었습니다. (아직 만들고 있어요.) 이렇게 다 나눠놓으니 속이 다 시원하네요! 아직 css 수정을 하지 않아서 겉모습은 그냥 단순 슬라이드인데 이번주에는 조금 더 예쁘게, 서비스 단계에 있는 페이지정도의 퀄리티로 꾸며보려고 해요. 꾸미는 건 사실 감각도 없고 어렵지만.. 흑흑...


### 후기?

사실 누군가에겐 별거 아닌 코드일 수 있지만 제겐 꽤 난이도 있었던(...) 첫 과제였습니다. 하지만 이런저런 과정들을 통해 잠깐 잊고 있던(벌써 잊었다니 놀랍지만) jQuery문법도 살펴보게 되고 새로운 메소드들을 많이 알게 되어 매우 즐거운 시간이었습니다. 문제가 안 풀려서 밤새서 풀고 하는건 수능 준비할 때나 그랬던 것 같은데 구글링으로 이런저런 문제와 해결법들을 찾아보면서 저와 같은 어려움을 겪는 사람이 많다는 것도 알게 되고, 또 그만큼 해답도 많다는 것을 알게 되어서 덜 외로웠어요. 아 참, 또 영어 공부를 열심히 해야겠다는 생각도 했습니다. 친절하게도 개념설명과 함께 원리를 알려주는 사람들이 많았는데 이해가 쉽게 되지 않아 한참을 읽었거든요. 그 시간만 단축 되었어도 조금 더 빨리 만들 수 있었을텐데... 언젠가 이 경험이 다 피와 살이 되었으면 좋겠네요. 아 재밌다! 자바스크립트 너무 재밌어요! 또 다음엔 뭘 만들면 좋을까요?   

쓸모있는 당근이 되길 바라며...
