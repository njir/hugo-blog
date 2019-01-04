+++
title = "React 개념 정리"
description = "책 및 강의 보고 정리한 react 기본 개념 및 함수"
tags = [
    "til",
    "react",
    "javascript"
]
date = "2018-12-31"
menu = "main"
+++


# React

## React 샘플 코드

```
import * as React from 'react';
import * as ReactDOM from 'react-dom';
import { Preview } from './Preview';

interface MyComponentProps {
	defaultName?: string;
}

interface MyComponentState {
	name?: string;
}

class MyComponent extends React.Component<MyComponentProps, MyComponentState> {

	constructor(props: MyComponentProps) {
    	super(props);
        this.state = {
        	name: props.defaultName,
        };
        this.handleInputChange = this.handleInputChange.bind(this);
    }
    
    // state 수정과 관련된 메소드
    private handleInputChange(event: React.FormEvent<HTMLInputElement>) {
    	event.preventDefault();
        this.setState({
        	name: event.currentTarget.value
        });
    }
    
    // 컴포넌트 라이프사이클
    public componentDidMount() {
    	console.log('mounted');
    }
    
    // 렌더링
    public render() {
    	return (
            <form>
            	<input value={this.state.name} onChange={this.handleInputChange} />
                    <Preview name={this.state.name} />
            </form>
        )
    }
}

const Preview = ({ name }) => {
	return <p>{`Hello ${name}!`}</p>;
};

ReactDOM.render(<MyComponent defaultName='Mike' />, document.getElementById('app'));
```

## React의 컴포넌트

- props와 state의 역할과 사용법 이해 필수
- 컴포넌트 생애주기의 관리 이해 필수

### 컴포넌트를 구성하는 요소

- React.Component
- constructor(): ES6부터 도입된 클래스의 생성자 메소드
    - React 컴포넌트에서는 주로 state의 초기 상태를 설정하는 데 사용
- state: 컴포넌트의 렌더링과 관계된 값을 관리하는 상태
- render(): 가상DOM의 렌더링 처리를 담당하는 메소드
- 클래스형 컴포넌트 vs 함수형 컴포넌트: 함수형 컴포넌트는 리액트의 라이프사이클을 관리할 수 없다.

```
const Preview = ({ name }) => {
	return <p>{`Hello ${name}!`}</p>;
};
```

### props

- 컴포넌트에 들어오는 입력
- 부모 컴포넌트에서 자식 컴포넌트로 전달

### state

- 컴포넌트 자체의 상태
- props와는 달리 state는 변경이 가능하다
- state를 변경할 때는 ```this.setState()``` 메소드 사용
- React는 클래스의 속성을 자유롭게 추가할 수 있다
	- 즉, ```render()```안에서 사용해야하는 경우에는 state로 추가해야 한다
	- 그게 아니라면 속성으로 관리


### props, state 구분방법
- props를 전달받는 자식 컴포넌트 쪽은 "본래 어떤 형태로 관리했었는지"는 잊고, 읽기 전용이라는 것만 유념해서 사용한다

#### 참고: 리액트 버전
- v16.3: ```getDerivedStateFromProps()```, ```getSnapshotBeforeUpdate()``` 메소드 추가
- v17: ```componentWillMount()```, ```componentWillReceiveProps()```, ```componentWillUpdate()``` 메소드 deprecated 됨

--- 

## 컴포넌트 라이프사이클

- 컴포넌트의 상태가 변화하는 것을 말함
- ```componentDidMount()```: 컴포넌트가 마운트 되었을 때 실행되는 메소드
- ```componentWillUnmount()```: 컴포넌트가 언마운트 될때
- ```componentWillReceiveProps()```: props를 전달받을 때 실행
- 최초 마운트 시점
  - constructor(props) -> componentWillMount() -> render() -> componentDidMount()
- 마운트 이후, props가 수정될때
    - componentWillReceiveProps(nextProps)
- 마운트 이후, state 수정될 때
    - shouldComponentUpdate(nextProps, nextState) -> (반환 값이 true일때) componentWillUpdate(nextProps, nextState) -> render() -> componentDidUpdate(prevProps, prevState)

### componentDidMount()
- 컴포넌트가 마운트될 때 딱 한번 실행되는 메소드
- 이 메소드의 처리 내용은 DOM이 존재한다고 가정
- css 프레임워크를 사용할 때 필요한 HTML 초기 처리는 이 메소드에서 수행하는 것이 좋다
- 이벤트리스너 정의나 HTTP 요청으로부터 데이터를 받아오는 처리를 호출하는 일 등

### componentWillUnmount()
- 컴포넌트가 언마운트 될 때 실행되는 메소드
- 언마운트란? 렌더링 되고 있는 컴포넌트가 페이지 이동등의 이유로 렌더링 대상에서 빠질 때 발생한다
- 타이머 해제나 비동기통신 취소 처리를 실행하는데 적합하다
- 이벤트리스너를 해제할 때

### componentWillReceiveProps()
- v17에서 deprecated 됨
- 마운트된 컴포넌트가 새로운 props를 받았을 때 실행
- 새로 수신된 props를 인자로 받는다
- props가 변경되는 것에 따라 state를 변경할 때 적합

```
public componentWillReceiveProps(nextProps) {
    if (this.props.name !== nextProps.name) {
        // do something
    }
}
```

### componentDidUpdate()
- 컴포넌트가 업데이트 되었을때 실행
- 변경되기 전의 props와 state를 인자로 받음
- 여기서 업데이트란 state나 props가 변경될 때
- 최초 마운트 시점에는 실행되지 않음

```
public componentDidUpdate(prevProps, prevState) {
    if (prevProps.name !== this.props.name) {
        // do something   
    }
}
```

### shouldComponentUpdate()
- 컴포넌트의 성능을 개선할 때 주목해야 할 메소드
- React 컴포넌트는 props, state가 변경되면 다시 render 호출
- 이 함수를 이용해서 re-rendering 제어 가능
- 불필요한 렌더링을 줄이면 성능 향상
- 업데이트된 후의 state와 props를 인자로 받음
- 비교시에는 deep comparison 

```
public shouldComponentUpdate(nextProps, nextState) {
    if (this.props.name !== nextProps.name) {
        // name이 바뀌었을 때만 re-rendering
        return true;
    }
    return false;
}
```

### componentDidCatch()
- v16부터 도입된 메소드
- 자식 컴포넌트에서 발생한 예외를 catch한 경우 실행