---
updated: 2018-01-25
title: React Router가 교환원이라면?
---

# 리액트 라우터가 교환원이라면?

> https://medium.freecodecamp.org/imagine-react-router-as-your-switchboard-operator-f4f1ac22188c 를 번역했습니다.

리액트 라우터는 구식 전화교환원과 아주 비슷합니다. 뒤에서 Single Page(SPA) React Application을 아주 부드럽게 조작해주죠. 사용자가 링크를 누르면, URL이 바뀌고, 뷰가 바뀌고 다른 페이지로 넘어갔음을 알 수 있게 되죠. 실제론 이동하지 않았는데 말이죠. 그와 같이 리액트 라우터는 리액트의 Virtual DOM을 아주 빠르게 렌더링하는 것으로, SPA를 Multi-page Application처럼 느끼게 해줍니다.

리액트 라우터를 이용하여 간단한 네비게이션을 만들어보겠습니다. 웹 어플리케이션을 만들기 위해  yarn add react-router-dom을 이용하겠습니다. 라우터 컴포넌트가 아주 많은 갈래로 퍼져있을테지만, 젠장할 친절한 설명을 위해선 컴포넌트들을 계층화해서 따라오게끔 해야겠죠. 아래 굵게 처리 된 컴포넌트들을 주의깊게 살펴보겠습니다ㅡ**Provider, BrowserRouter, Switch, Route**

    import React from 'react';
    import ReactDOM from 'react-dom';
    import { Provider } from 'react-redux';
    import { createStore, applyMiddleware } from 'redux';
    import { BrowserRouter, Route, Switch } from 'react-router-dom';
    import promise from 'redux-promise';
    import { PostsNew, PostsShow, PostsIndex } from './components'
    const createStoreWithMiddleware = applyMiddleware(promise)(createStore);
    ReactDOM.render(
     <Provider store={createStoreWithMiddleware(reducers)}>
      <BrowserRouter>
       <div>
        <Switch>
         <Route path="/posts/new" component={ PostsNew } />
         <Route path="/posts/:id" component={ PostsShow } />
         <Route path="/" component={ PostsIndex } />
        </Switch>
       </div>
      </BrowserRouter>
     </Provider>
    , document.getElementById('root'));

## 교환원...aka \<Provider />

만약에 전화를 끊고, 다시 전화 재다이얼을 하고 싶으면 어떻게 할까요(페이지 새로고침)? 다시 이전으로 돌아가 전화를 걸었던 가장 최근 친구에게 다시 전화를 걸고 싶다면요(뒤로가기)? 걱정마세요. 교환원ㅡaka Provider가 해결해줄겁니다. 모든 브라우징 히스토리를 기록하고 있는 Redux의 store의 힘으로 말이죠. store를 모든 리액트 라우터 컴포넌트들이 마음 편히 끊을 수 있게 도와주는 교환원(그리고 부모 컴포넌트)정도라고 생각해두세요.

    <Provider store={createStoreWithMiddleware(reducers)}>



## 전화 또는 전신?...aka <BrowserRouter />

전화기 만으로는 전화를 걸 수 없죠. 이와 비슷하게 네이티브와 웹 사이에도 올바른 라우터가 있어야만 합니다. 대부분의 웹앱들은 BrowserRouter를 임포팅하는 것으로 HTML5 브라우저들이 동작하게 합니다.

## 누구?…aka \<Switch />

교환원은 사실 엄청 게으릅니다. 손가락을 넘겨 전화번호부에서 찾은 첫번째 사람과 바로 전화를 연결해주는거죠. URL당 하나의 컴포넌트를 렌더링하고 싶을 때, Switch는 배타적 렌더링을 허락합니다(뒤에선 정규표현식으로 매칭을 확인하고 있을거고요). 이게 바로 순서를 정리할 때 주의해야할 점이 되겠죠. 구체적일수록 제일 먼저 쓰고, 일반적일수록 마지막에 둬야합니다.

 path="/"를 제일 먼저 두지 마세요. 전화 받느라 집 밖에 나갈 수 없을걸요.

    <Switch>
      <Route path="/posts/new" component={ PostsNew } />
      <Route path="/posts/:id" component={ PostsShow } />
      <Route path="/" component={ PostsIndex } />
     </Switch>



## 전화번호...aka \<Route />

그나저나 교환원이 전화번호를 쓰던가요? 그건 차치하고... Route컴포넌트는 URL과 match하도록 설정한 특정 컴포넌트를 렌더링 합니다. 아래 예문의 :id를 한번 보세요. 리액트 라우터의 match 객체를 이용하여, : 뒤에 오는 모든 것들은 렌더링 된 컴포넌트 내에서 액세스 됩니다. 예를 들어 this.props.match.params.id

    <Route path="/posts/:id" component={ PostsShow } />

## 재다이얼...aka \<Link />

이 전화는 점점 쓸모없어지게 됩니다. 그냥 빨리 다른 사람한테 전화를 걸던가 집으로 돌아가죠. 리액트 라우터에서 Link컴포넌트로(HTML 앵커 태그로 깔쌈하게 싸여있는), 홈 버튼이나 다른 컴포넌트를 통해 앵커 태그처럼 쓸 수 있습니다. 어디로 갈 지 to 필드를 채운 뒤, inline으로 스타일을 채워 넣거나 버튼 태그를 추가해서 싸도 되겠군요.

    import { Link } from 'react-router-dom';
    class YourThing extends Component {
       render() {
         return(
           <div>
            <Link to="/"> Home </Link>
           </div>
         )
       }
    }

지금까지 그냥 몇가지 아주 기초적인 개념들을 살펴봤습니다. 당연히 더 많은 개념들이 존재하고 있으니 멋진 문서들과 튜토리얼로 여러분들의 라우트를 무한히 커스터마이징하길 바랍니다. 




