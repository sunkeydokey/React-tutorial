# Introduction

[자습서: React 시작하기](https://ko.reactjs.org/tutorial/tutorial.html)를 통해 React 학습 보충

# 개요

## React란 무엇인가요?

React란 사용자 인터페이스를 구축하기 위한 선언적 방식의 JS 라이브러리이다.  
`컴포넌트`라는 독립된 코드 파편을 이용해 UI를 구성한다.  
XML과 유사한 JSX를 사용한다. 컴포넌트를 사용해 React에 화면에 표현할 것을 알려준다. React는 데이터가 변경될 때 컴포넌트를 업데이트하여 다시 렌더링한다.

```JavaScript
class ShoppingList extends React.Component { // React 컴포넌트 클래스 ShoppingList
  render() { // 개별 컴포넌트는 props라는 매개변수를 받아오고 render 함수를 통해 표시할 뷰 계층 구조를 반환
    return (
      <div className="shopping-list">
        <h1>Shopping List for {this.props.name}</h1>
        <ul>
          <li>Instagram</li>
          <li>WhatsApp</li>
          <li>Oculus</li>
        </ul>
      </div>
    );
  }
}
```

render 함수는 화면에서 보고자 하는 내용을 반환한다. React는 설명을 전달받고 결과를 표시한다. 위의 코드는 빌드 시점에 다음과 같이 변화한다.

```JavaScript
React.createElement(
  'div',
  {
    className: 'shopping-list',
  },
  React.createElement(
    'h1',
    null,
    'Shopping List for ',
    props.name
  ),
  React.createElement(
    'ul',
    null,
    React.createElement('li', null, 'Instagram'),
    React.createElement('li', null, 'WhatsApp'),
    React.createElement('li', null, 'Oculus')
  )
);
```

JSX는 내부의 중괄호 안에 JavaScript 표현식을 사용할 수 있다. React 엘리먼트는 JS 객체이며 변수에 저장하거나 프로그램 내부에서 전달할 수 있다.

## 초기 코드 살펴보기

초기 코드 [src/index.js](./src/index.js)에는 세 가지 React 컴포넌트(Square-버튼, Board-Square 9개, Game-게임판)를 확인할 수 있다.

## Props를 통해 데이터 전달하기

부모 컴포넌트인 Board 에서 자식 컴포넌트인 Square로 prop을 전달하기  
Square에 `value` prop을 전달할 수 있게 Board의 renderSquare 함수의 반환값을 `<Square value={i} />`로 수정한다.  
값을 표시하기 위해 Square의 render 함수에서 `{/* TODO */}`를 `{this.props.value}`로 수정한다.

```JavaScript
class Square extends React.Component {
  render() {
    return <button className="square">{this.props.value}</button>;
  }
}

class Board extends React.Component {
  renderSquare(i) {
    return <Square value={i} />;
  }

  render() {
    const status = 'Next player: X';

    return (
      <div>
        <div className="status">{status}</div>
        <div className="board-row">
          {this.renderSquare(0)}
          {this.renderSquare(1)}
          {this.renderSquare(2)}
        </div>
        <div className="board-row">
          {this.renderSquare(3)}
          {this.renderSquare(4)}
          {this.renderSquare(5)}
        </div>
        <div className="board-row">
          {this.renderSquare(6)}
          {this.renderSquare(7)}
          {this.renderSquare(8)}
        </div>
      </div>
    );
  }
}
```

## 사용자와 상호작용하는 컴포넌트 만들기

Square 컴포넌트를 클릭하면 X가 체크되도록 만들기

```JavaScript
class Square extends React.Component {
  constructor(props) { // 클래스에 생성자를 추가하여 state 초기화
    super(props);
    this.state = {
      value: null,
    };
  }
  ...
}
```

> 모든 React 컴포넌트 클래스는 생성자를 가질 때 super(props) 호출 구문부터 작성해야한다.

```JavaScript
class Square extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: null,
    };
  }

  render() {
    return (
      <button className="square" onClick={() => this.setState({ value: 'X' })}>
        {this.state.value}
      </button>
    );
  }
}
```

`<button>` 태그의 this.props.value를 this.state.value 로 변경  
click 이벤트 핸들러를 `{() => this.setState({ value: 'X' })}`로 변경</br></br>

Square의 render 함수 내부에서 이벤트 발생 시에 this.setState를 호출해 클릭될 때마다 Square가 다시 렌더링되도록 React에 알릴 수 있다.
