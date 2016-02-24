---
title: 첫 개인 과제(2)
updated: 2016-02-24
---

지난 편에선 api를 이용하여 제가 원하는 프로그램 구현을 위해서 어떤 값들을 불러오면 되는지에 대한 정보만을 확인했습니다. 이번 편에는 본격적으로 JSON 데이터를 어떻게 처리했는지 적어보려고 합니다.

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

$http.get('https://kr.api.pvp.net/api/lol/kr/v1.2/champion?freeToPlay=true&api_key=69d7f34e-d9f8-4f80-b30e-1cb2d46c1132').
        success(function(data){
            $scope.champions = data.champions;
            for(var i = 0; i < data.champions.length; i++) {
    $http.get('https://global.api.pvp.net/api/lol/static-data/kr/v1.2/champion/'+ $scope.champions[i].id +'?api_key=69d7f34e-d9f8-4f80-b30e-1cb2d46c1132').
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
또 다른 문제는 for문 내 변수 i였어요. i를 그대로 전달하면 내부에 있는 $http.get()가 받는 url에 알맞은 i값(제가 기대한 것은 0부터 9까지의 값이 차례대로 들어가는 것이었습니다.)이 들어가 ```$scope.champions[i].id```가 id별로 JSON 데이터를 하나씩 불러올 수 있을거라고 생각했거든요. i는 0부터 차례대로 i++ 된다고 써놨으니까요! 하지만 이 또한 제 뜻대로 되지 않았습니다. console.log로 확인해보니 $http.get내로 전달될 때 무조건 i값이 10으로만 전달되더라고요. 아무래도 JSON 내 JSON을 받는 것과 관련이 있는 것 같아 검색을 해보니 [How to retrieve multiple JSON objects with a for loop in javascript](http://stackoverflow.com/questions/16380714/how-to-retrieve-multiple-json-objects-with-a-for-loop-in-javascript)와 같은 글이 있어 이를 참고하여 코드를 수정했습니다. 


