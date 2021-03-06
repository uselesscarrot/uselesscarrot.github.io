---
title : 첫 개인 과제
updated : 2016-02-18
---

## 첫 개인 과제 : League of Legends Free Rotation Champions 소개 페이지 만들기

(타이틀을 너무 길게 썼더니 아예 제목이 없는걸로 뜨길래 본문에 추가하였어요.)

회사에 들어와서 html/css 수업을 처음 받고나서 이미지와 몇가지 기능에 대한 설명만 가지고 간단한 페이지를 만드는 과제가 있긴 했지만 "작은거라도 좋으니 제가 없는 동안 뭔가 만들어보세요."하는 말과 함께 남은 과제를 하는건 쉬운 일이 아니었습니다. 정말 온전히 내 스스로 다 해야하는 과제! 라는 느낌.
일단 제가 무엇을 할 줄 아는지 모른다는 것과 무엇을 아는지 모른다는 것이 가장 큰 장벽입니다. 3개월 간 html/css/javascript(jQuery, AngularJS)를 공부했지만 손에 잡힐 듯 잡히지 않는 느낌이라 이걸로 무엇을 할 수 있을지 가늠이 잘 안 됐어요. 일단 책만 보고 쭉 따라했었으니까 각각 어떻게 조립해야 멋진게 나올지 상상도 잘 안 되고 응용력이 많이 떨어지는 느낌? 중고등학생 때 수학 교과서 2점짜리 기본 문제만 신나게 풀다가 4점짜리 응용문제를 만난 느낌이라고 해야되나?
   
처음에는 관리자 페이지를 만들려고 했습니다. 언젠가 만들어야 하는 페이지이기도 하고, 겸사겸사 과제도 겸 업무 겸. 그런데 이 생각을 할 때까지만 해도 제가 그걸 만들 깜냥이 안 된다는걸 몰랐죠. 호호...
한땀 한땀 한 픽셀 한 픽셀 옮겨가며 모양을 만들고 있는데 사수가 와서 "당근씨 그렇게 하면 3주동안 아무것도 못 만들어요."했습니다.
그럼 그렇지. html/css 책을 처음부터 다 따라하면서 익혀서 그런지 내가 필요한 기능들을 누군가 구현해서 공개해놨다고는 상상도 못했어요.
이미 템플릿도 많고 기능별로 필요한 Snippet들이 구글에 검색만 하면 언어별로 프레임워크별로 날아다니고 있었습니다. 엉엉.
그래서 관리자 페이지를 포기했습니다. (???)   
사실 예쁘게만 만들면 그걸로 OK라고 생각한 안일한 나도 문제였지만 무엇보다도 데이터를 어떻게 처리해야하는지 모르는 내가 가장 큰 문제..
   
그 다음으로는 일단 JSON을 어떻게 처리하고 내가 원하는 방식으로 어떻게 다룰 수 있는지를 배우는게 우선일 것 같아 정말 "작은거"를 만들어보기로 했습니다.
외부 API를 이용해서 원하는 프로그램을 뚝딱뚝딱 만드는게 항상 멋져보였으므로, 그리고 제가 좋아하는 데이터를 가지고 하면 더 흥미가 생길 것 같아 League of Legends의 Free Rotation Champion들을 소개하는 페이지를 만들기로 했습니다.
   
#### League of Legends의 Free Rotation Champions란?   
   
League of Legends(이하 롤)의 경우 150여개가 넘는 챔피언들을 플레이 할 수 있으며 매 판마다 그 중 단 하나의 챔프만을 골라 게임에 참가할 수 있습니다.
게임의 결과로 얻은 IP(게임 내 사이버 머니)로 챔피언들을 구매해서 하고 싶은 챔피언을 플레이 할수도 있지만, 롤이 매주 특정 요일에 무료로 플레이 할 수 있게 제공해주는 10개(명? 마리?)의 챔피언 중 하나를 고를 수도 있습니다.(일종의 체험판?) 이를 Free Rotation Champions라고 합니다.
그렇다보니 보유 챔피언수가 적거나 IP가 적은 사람들은 10개의 무료 제공 챔피언들을 기다릴 수 밖에 없습니다. 지금의 저는 원하는 챔피언을 거의 다 가지고 있긴 하지만, 신규 챔피언이 나올 때 무작정 사기보다 프리 로테이션 데이를 기다렸다가 신 챔프가 나오면 한번 플레이 해본 뒤 내 플레이 스타일과 맞으면 구매를 하는 타입이기 때문에 제게도 유용할 것이라고 판단했습니다.
구구절절 썼지만 전 게임이 좋고, 롤이 좋고, 게임 내 프리 로테이션 정보가 궁금해서 만들었습니다. (이미 이런 정보를 제공해주는 페이지가 있는 것 같긴 하지만...)
   
#### 데이터 구하기
   
이를 위해 먼저 [Riot Games Developer Page](https://developer.riotgames.com/)에 접속하여 제 롤 아이디로 개발자 등록을 하고 api key를 발급 받았습니다.
FULL API REFERENCE 페이지로 들어가 <한국 지역>의 <최신 버전> API를 살펴봅니다.
champion-v1.2를 보니 freeToPlay의 쿼리값을 true로 맞춰주면 지금 이 순간 무료로 플레이 할 수 있는 챔피언 10개의 리스트를 JSON으로 내보내주네요.   
Request URL은 다음과 같습니다. : https://kr.api.pvp.net/api/lol/kr/v1.2/champion?freeToPlay=true&api_key=API키값

{"champions": [
   {
      "botMmEnabled": true,
      "id": 81,
      "rankedPlayEnabled": true,
      "botEnabled": true,
      "active": true,
      "freeToPlay": true
   },
   {
      "botMmEnabled": false,
      "id": 114,
      "rankedPlayEnabled": true,
      "botEnabled": false,
      "active": true,
      "freeToPlay": true
   },
   ...
   }]
}
   
JSON 데이터를 보니 챔피언명이 바로 나오는 것이 아니라 각 챔피언별로 id값이 따로 나오고 있어, id값에 맞는 챔피언명을 다시 알아내야 할 것 같습니다.
이를 위해 lol-static-data-v1.2의 id를 파라미터로 받는 Request URL을 이용합니다. : https://global.api.pvp.net/api/lol/static-data/kr/v1.2/champion/{ID값}?api_key=API키값

{
   "id": 81,
   "title": "방탕한 탐험가",
   "name": "이즈리얼",
   "key": "Ezreal"
}
   
위에서 첫번째로 나온 81번 챔피언은 이즈리얼이었네요.
   
이미지도 필요하니 이미지를 구해보겠습니다. 이미지를 어떻게 구하면 좋을까, 구글에서 아무 이미지나 다운받아서 쓸까? 하다가 150여개의 챔프를 하나하나 다 검색/저장 하는 것도 큰 일이 될 것 같다는 생각에 롤이 제공하는 이미지를 살펴보기로 했습니다.
다행히 이미 라이엇에서 http://ddragon.leagueoflegends.com/cdn/img/champion/splash/챔피언명_0.jpg 과 같은 경로로 모든 챔피언들의 이미지를 저장해두고 있었습니다. 변할 일도 없으니 이 주소를 따서 쓰면 될 것 같습니다.
그러나 경로명에 한글이 들어갈 순 없을 것입니다. 흑흑. 
그래서 전 위 주소에서 Region부분(KR)을 북미인 NA로 바꾸고 재 검색을 하여 name을 따오기로 생각했습니다. : https://global.api.pvp.net/api/lol/static-data/NA/v1.2/champion/{ID값}?api_key=API키값 
**하지만 전 바보였던 거예요.** 이미 한국지역 데이터에서도 key값에 영문명이 나와있는데 request를 또 하고 있었습니다.
어차피 저 혼자 만들면서 쓰는 API 요청이라 별 상관 없겠지만 요청이 많아지면 제 API값도 리밋에 걸려 막힐 수 있으니 조심해야겠죠. 프로그램도 여러번 쓸데없는 행동을 반복할테니 주의해야겠고요.
   
아무튼, 위 JSON 데이터에서 name값과 key값을 이용해서 캐릭터명과 key값에 맞는 이미지 경로까지 모두 얻을 수 있었습니다.
이제 이것을 어떻게 Javascript로 구현하느냐가 문제네요. 사실 책으로는 배웠어도 실제 존재하는 데이터로 뭔가 해본 적이 없어서 와닿지 않았었거든요.
하지만 제가 모르는 건 항상 다른 사람도 모르더라고요. 하핫? 스택오버플로우에 들어가서 큰 도움을 얻어 일단 코드를 짜봤습니다.
깨끗한 코드에 대한 개념이란게 없어서 잘 모르겠지만 잘 모르는 제가 봐도 좀 지저분한 코드인 것 같습니다(...)

는 다 짜고 또 이어서 써보겠습니다.
