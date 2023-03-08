# Introduction

[자습서: React 시작하기](https://ko.reactjs.org/tutorial/tutorial.html)를 통해 React 학습 보충

# 개요

## React란 무엇인가요?

React란 사용자 인터페이스를 구축하기 위한 선언적 방식의 JS 라이브러리이다.  
`컴포넌트`라는 독립된 코드 파편을 이용해 UI를 구성한다.  
XML과 유사한 JSX를 사용한다. 컴포넌트를 사용해 React에 화면에 표현할 것을 알려준다. React는 데이터가 변경될 때 컴포넌트를 업데이트하여 다시 렌더링한다.

```JS
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

```JS
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

JSX는 내부의 중괄호 안에 JS 표현식을 사용할 수 있다. React 엘리먼트는 JS 객체이며 변수에 저장하거나 프로그램 내부에서 전달할 수 있다.

## 초기 코드 살펴보기

초기 코드 [src/index.js](./src/index.js)에는 세 가지 React 컴포넌트(Square-버튼, Board-Square 9개, Game-게임판)를 확인할 수 있다.

## Props를 통해 데이터 전달하기

부모 컴포넌트인 Board 에서 자식 컴포넌트인 Square로 prop을 전달하기  
Square에 `value` prop을 전달할 수 있게 Board의 renderSquare 함수의 반환값을 `<Square value={i} />`로 수정한다.  
값을 표시하기 위해 Square의 render 함수에서 `{/* TODO */}`를 `{this.props.value}`로 수정한다.

```JS
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

```JS
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

```JS
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

# 게임 완성하기

## State 끌어올리기

게임의 State를 자식 컴포넌트인 Square에서 유지하고 있다. 각각의 Square가 아니라 부모 컴포넌트 Board에서 게임의 상태를 관리하도록 하는 것이 가장 좋은 방법이다. 여러개의 자식으로부터 데이터를 모으거나 두 개의 자식 컴포넌트들이 서로 통신하게 하려면 부모 컴포넌트에 공유 state를 정의해야 한다.</br>

```JS
class Board extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      squares: Array(9).fill(null),
    };
  }
}
```

Board에 생성자를 추가하고 9개의 사각형을 만드는 9개의 null 배열로 초기 state 생성

```JS
renderSquare(i) {
    return (
      <Square
        value={this.state.squares[i]}
        onClick={() => this.handleClick(i)}
      />
    );
  }
```

Board에서 Square로 value prop, onClick prop을 전달

```JS
class Square extends React.Component {
  render() {
    return (
      <button className="square" onClick={() => this.props.onClick()}>
        {this.props.value}
      </button>
    );
  }
}
```

1. Square의 render 함수 내부의 this.state.value를 this.props.value로 수정한다.
2. Square의 render 함수 내부의 this.setState()를 this.props.onClick()으로 수정한다.
3. Square는 게임의 상태를 유지할 필요가 없기 때문에 생성자를 제거한다.

```JS
  handleClick(i) {
    const squares = this.state.squares.slice();
    squares[i] = 'X';
    this.setState({ squares: squares });
  }
```

Square를 클릭하면 this.props.onClick()을 호출하고, 이는 Board에서 정의 되었다. Board에서 넘겨받은 onClick함수는 handleClick(i)이므로 handleClick 함수를 작성한다.

> Square 컴포넌트는 이제 state를 유지하지 않으며 Board 컴포넌트에서 값을 받아 클릭될 때 Board로 정보를 전달한다. React에서는 Square 컴포넌트를 `제어되는 컴포넌트` 라고 한다.

## 함수 컴포넌트

Square를 함수 컴포넌트로 변경하기

함수 컴포넌트를 통해 더 간단하게 컴포넌트를 작성할 수 있다. state를 따로 지정하지 않고 render함수만을 가진다.

```JS
function Square(props) {
  return (
    <button className="square" onClick={props.onClick}>
      {props.value}
    </button>
  );
}
```

## 순서 만들기

```JS
  constructor(props) {
    super(props);
    this.state = {
      squares: Array(9).fill(null),
      xIsNext: true,
    };
  }

  handleClick(i) {
    const squares = this.state.squares.slice();
    squares[i] = this.state.xIsNext ? 'X' : 'O';
    this.setState({
      squares: squares,
      xIsNext: !this.state.xIsNext,
    });
  }
```

Board의 초기 state에 xIsNext를 true로 설정한 후 handleClick이 호출 될 때마다 Boolean 값을 바꾸도록 수정하여 클릭마다 순서를 바꾸도록 할 수 있다.

```JS
  render() {
    const status = `Next player: ${this.state.xIsNext ? 'X' : 'O'}`;

    return (...
    )
  }
```

박스 상단의 Next player 도 변경되도록 render 함수도 수정

## 승자 결정하기

로직에 따른 코드 수정, 이미 클릭한 Square를 클릭하면 무시하도록 수정

# 시간여행 추가하기

## 동작에 대한 기록 저장하기

square 배열을 불변 객체로 취급하고 history에 slice()를 사용해 만들어지는 복사본을 저장한다.

## 다시 State 끌어올리기

[State 끌어올리기](##State-끌어올리기)에서 처럼 Game 컴포넌트에서 history state와 square state를 모두 유지하도록 끌어올린다.

```JS
  constructor(props) {
    super(props);
    this.state = {
      history: [{ squares: Array(9).fill(null) }],
      xIsNext: true,
    };
```

```JS
  renderSquare(i) {
    return (
      <Square
        value={this.props.squares[i]}
        onClick={() => this.props.onClick(i)}
      />
    );
  }
```

1. Board에서 생성자 제거
2. Board의 renderSquare 안의 this.state.squares[i]를 this.props.squares[i]로 수정
3. Board의 renderSquare 안의 this.handleClick(i)을 this.props.onClick(i)으로 수정

```JS
  render() {
    const history = this.state.history;
    const current = history[history.length - 1];
    const winner = calculateWinner(current.squares);
    let status;
    if (winner) {
      status = 'Winner: ' + winner;
    } else {
      status = 'Next player: ' + (this.state.xIsNext ? 'X' : 'O');
    }

    return (
      <div className="game">
        <div className="game-board">
          <Board
            squares={current.squares}
            onClick={(i) => this.handleClick(i)}
          />
        </div>
        <div className="game-info">
          <div>{status}</div>
          <ol>{/* TODO */}</ol>
        </div>
      </div>
    );
  }
```

Game 컴포넌트의 render 함수가 최신 state를 업데이트. Game 컴포넌트가 게임의 상태를 렌더링하게 되어 Board의 render함수에서 중복된 코드를 제거할 수 있다. 또한 handleClick 함수도 Game 컴포넌트로 옮겨주고 Game 컴포넌트의 state 구성에 따라 수정한다.

```JS
  handleClick(i) {
    const history = this.state.history;
    const current = history[history.length - 1];
    const squares = current.squares.slice();
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    squares[i] = this.state.xIsNext ? 'X' : 'O';
    this.setState({
      history: history.concat([{
        squares: squares,
      }]),
      xIsNext: !this.state.xIsNext,
    });
  }
```

## 과거의 이동 표시하기

```JS
render() {
    const history = this.state.history;
    const current = history[this.state.stepNumber];
    const winner = calculateWinner(current.squares);

    const moves = history.map((step, move) => {
      const desc = move ? `Go to move #${move}` : 'Go to game start';
      return (
        <li>
          <button onClick={() => this.jumpTo(move)}>{desc}</button>
        </li>
      );
    });

    let status;
    ...

    return (
      <div className="game">
        ...
        <div className="game-info">
          <div>{status}</div>
          <ol>{moves}</ol>
        </div>
      </div>
    );
  }
}
```

`Array.map((value, index)=>{})`를 사용해 현재 history 값인 step과 인덱스인 move를 참조한다. 게임 시작 시 move(index)는 0 이므로 false로 변환되어 'Go to game start'가 첫번째 li 요소로 추가될 것이고, 클릭마다 버튼을 가진 li 요소를 추가한다.

## Key 선택하기

개발자 콘솔을 보면 li 요소들이 추가되면서부터 key prop에 관한 경고메세지가 출력된다. React는 li들 간의 순서, 추가, 제거 등 프로그래머가 의도한 바를 바로 알지 못하기 때문에 key prop을 지정하여 각 li가 서로 다르다는 것을 의도적으로 알려주어야 한다.  
전역적으로 고유한 key까지는 필요없으나 컴포넌트와 관련 아이템 사이에서는 고유한 값을 가져야 한다.

```JS
const moves = history.map((step, move) => {
      const desc = move ? `Go to move #${move}` : 'Go to game start';
      return (
        <li key={move}>
          <button onClick={() => this.jumpTo(move)}>{desc}</button>
        </li>
      );
    });
```

현재 코드에서 move는 이동에 따라 순차적이고 고유한 숫자를 가진다. 따라서 li요소에 key={move}를 추가하여 prop을 넘겨주면 배열 요소에 고유한 key prop이 생긴다.

## 시간 여행 구현하기

```JS
class Game extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      history: [{
        squares: Array(9).fill(null),
      }],
      stepNumber: 0,
      xIsNext: true,
    };
  }
```

Game 컴포넌트의 state에 stepNumber를 추가해준다.

```JS
  jumpTo(step) {
    this.setState({
      stepNumber: step,
      xIsNext: (step % 2) === 0,
    });
  }
```

step이 짝수인 경우 X가 다시 턴을 받는 게임이므로 돌아간 step에서 다시 같은 플레이어가 차례를 받을 수 있도록 한다.

```JS
  handleClick(i) {
    const history = this.state.history.slice(0, this.state.stepNumber + 1);
    const current = history[history.length - 1];
    const squares = current.squares.slice();
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    squares[i] = this.state.xIsNext ? 'X' : 'O';
    this.setState({
      history: history.concat([{
        squares: squares
      }]),
      stepNumber: history.length,
      xIsNext: !this.state.xIsNext,
    });
  }
```

this.state.history를 this.state.history.slice(0, this.state.stepNumber + 1)로 수정하여 뒤로 돌아간 후 새로운 수를 두었을 때 그 이후의 기록을 모두 삭제할 수 있도록 한다.

```JS
  render() {
    const history = this.state.history;
    const current = history[this.state.stepNumber];
    const winner = calculateWinner(current.squares);

    ...
  }
```

Game 의 render함수를 수정하여 stepNumber에 맞는 이동을 렌더링하도록 한다.
