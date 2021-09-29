# Flux 아키텍처

## MVC 아키텍처의 한계

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d3097f61-d51c-4e87-ad77-811eb533ff20/Untitled.png)

Controller가 Model의 데이터를 조회, 업데이트 하고 Model은 이 데이터를 View를 통해 반영한다. View는 사용자로부터 데이터를 입력받기도 해서 반대로 Model에 영향을 주기도 한다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bd5e2fce-6840-457d-8588-2226aefa0674/Untitled.png)

이러한 Model ↔  View의 양방향 구조 때문에 어플리케이션이 거대해지면 복잡해지게 된다. 이렇게 복잡한 구조를 갖게 되면, 의존성의 이유로 View의 변경에 따라 여러 Model을 업데이트 해줘야 하는 경우가 생긴다.

## 해결책으로 나온 Flux 아키텍처

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b1a8afb4-3f53-4f7c-b1c3-0da7816f641d/Untitled.png)

Flux 아키텍처는 단방향 데이터 흐름을 가진다. Dispatcher → Store → View의 흐름이고, View에서 데이터의 입력이 생기면 Action을 통해 Dispatcher에 반영한다. 따라서 데이터가 입력되면 처음부터 다시 시작되는 방식이다.

### Dispatcher

Dispatcher는 데이터의 흐름을 관리하는 중앙 허브 역할이다. 액션의 발생을 통해 메시지나 액션 객체가 Dispatcher로 전달되고, Dispatcher는 콜백 함수를 통해 이를 Store로 전달한다.

### Store

Store는 어플리케이션의 데이터와 비지니스 로직을 갖고있고, 상태를 저장한다. 모든 상태 변경이 Store에 의해 결정되지만, 상태변경을 위한 요청을 스토어에 직접 보낼 수 없기에, Action을 보내 Dispatcher를 거쳐 Store에 요청해야 한다.

### View / Controller View

View는 변경된 상태를 전달받아 사용자에게 보여주고, 사용자로부터 입력 받을 화면을 보여준다.

Controller View는 Store와 View 사이의 중간관리자같은 역할을 하며, 상태가 변경되었을 때 Store가 그 사실을 알려주면, Controller View는 자신 아래에 있는 모든 View에게 새로운 상태를 넘겨준다.

### Action

Action은 Dispatcher에 전달되는 데이터의 묶음이다.전달할 Action 객체는 Action creator(액션 생성자)라는 함수를 통해 만들어진다.

# Redux

Redux는 위에서 설명한 Flux 아키텍처의 구현체 중 하나인데, 가볍고 사용법이 간단하다는 장점으로 가장 많이 사용된다. 자바스크립트 애플리케이션에서 흔히 쓰이는 state container이며, React 뿐만 아니라, 어떠한 자바스크립트 라이브러리와도 연결할 수 있는 상태 관리 라이브러리이다.

## 필요성

**Without Redux**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b7f77fb0-b73c-4ef9-a5ba-261e1420c5c9/Untitled.png)

Root 컴포넌트에는 something 이라는 상태 값이 있고, onDoSomething 이라는 함수가 something 값에 변화를 준다. onDoSomething 은 Root -> B -> H 로 전달되고, H 에서 이벤트가 발생하여 이 함수가 호출되면 something 이 Root -> A -> E -> F 로 전달된다.

props 가 필요한 곳으로 제대로 전달되게 하기 위하여, 실제로는 해당 props 를 사용하지 않는 컴포넌트를 거쳐가야 하기에 비효율적이다.

**With Redux**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5f42154c-0000-490e-949e-16f4e39599ce/Untitled.png)

앱이 지니고 있는 상태와, 상태 변화 로직이 들어있는 스토어를 통해 원하는 컴포넌트에 원하는 상태값과 함수를 직접 주입해줄 수 있게 된다.

## 사용법

### Action

상태에 어떠한 변화가 필요하게 될 때 발생시키는 액션은 하나의 객체로 표현되는데, 액션 객체는 다음과 같은 형태를 가진다.

```jsx
{
  type: "TOGGLE_VALUE"
}
```

액션 객체는 `type` 필드를 필수적으로 가지고 있어야하고 그 외의 값들은 옵션이다.

```jsx
{
  type: "ADD_TODO",
  data: {
    id: 0,
    text: "리덕스 배우기"
  }
}
```

### Action Creator

액션 생성자는 action을 만드는 함수이다. 파라미터를 받아서 액션 객체 형태로 만드는 역할을 한다.

```jsx
function addTodo(data) {
  return {
    type: "ADD_TODO",
    data
  };
}

const changeInput = text => ({
  type: "CHANGE_INPUT",
  text
});
```

### Reducer

리듀서는 변화를 일으키는 함수이다. 리듀서는 두가지의 파라미터인 1) 현재 상태와, 2) 액션을 받아 새로운 상태를 만들어서 반환한다.

```jsx
function reducer(state, action) {
  // 상태 업데이트 로직
  return alteredState;
}
```

### Store

리덕스에서는 한 애플리케이션 당 하나의 스토어를 만들게 된다. 스토어 안에는 현재의 state와, reducer가 들어가있고, 추가적으로 몇가지 내장 함수들이 있다.

```jsx
const store = createStore(reducer);
```

### dispatch

디스패치는 스토어의 내장함수 중 하나이다. 발생한 action을 파라미터로 받아 스토어로 전달하면, 스토어는 reducer를 실행시켜서 해당 action을 처리하는 로직이 있다면 이를 참고해 새로운 상태를 만들어준다. 

```jsx
btn1.onclick = () => {
  store.dispatch(action1());
}

btn2.onclick = () => {
  store.dispatch(action2());
}
```

### subscribe

구독 또한 스토어의 내장함수 중 하나이다. subscribe 함수는, 함수 형태의 값을 파라미터로 받는다. subscribe 함수에 특정 함수를 전달해주면, action이 dispatch에 입력될 때 마다 전달해준 함수가 호출된다.

```jsx
store.subscribe(render);
```