## todoList

투두리스트
[벨로퍼트님 블로그](https://velopert.com/3480) 보고 연습하고 정리

#### 함수형 컴포넌트

```js
const TodoListTemplate = ({ form, children }) => {
  return (
    <main className="todo-list-template">
      <div className="title">오늘 할 일</div>
      <section className="form-wrapper">{form}</section>
      <section className="todos-wrapper">{children}</section>
    </main>
  );
};
```

* 비구조화 할당 `(props) => { ... }` -> `({form, children}) => { ... }`
  * `props.form` -> `form`

#### Template 컴포넌트

보통방식

```js
<TodoListWrapper>
  <Form />
  <TodoList />
</TodoListWrapper>
```

* `Form`과 `TodoList` 사이에 테두리를 설정할때 보통방식은 `Form`,`TodoList` 컴포넌트에 스타일을 넣어야 한다.
* `Template` 컴포넌트를 사용시에는 `Template`에 스타일을 주면 된다.

#### 클래스형 컴포넌트(성능 최적화)

* `리스트`를 렌더링하게 될 때는 특히 동적일 경우에는 함수형이 아닌 클래스형 컴포넌트로 작성해야 한다.
* 클래스형 컴포넌트로 작성해야 상태 및 라이프사이클를 사용할 수 있다.

#### JSX 조건문

```js
  <div classNames={`todo-text ${checked && 'checked'}`}>
```

* `&&` 앞이 참이면 뒤에꺼 리턴
  * `${checked && 'checked'}` 앞 checked 가 true 이면 'checked' 반환, false 이면 false 반환
* `cheked` 값이 있을때(true 일때) "todo-text checked"
* `cheked` 값이 없을때(false 일때) "todo-text false"

#### `e.stopPropagation()`

```js
<div
  className="remove"
  onClick={e => {
    e.stopPropagation();
    onRemove(id);
  }}>
  &times;
</div>
```

* `e.stopPropagation()`이 없으면 x(remove)를 클릭하면 부모인`todo-item`클래스인 `onToggle(id)`가 실행된다.
* 이걸 방지하는게 `e.stopPropagation()` 이 메소드이다. 이벤트의 `확산`을 멈춰줍니다.
* `onRemove`만 실행되고 `onToggle`은 실행되지 않게 합니다.

#### 함수, 렌더링

```js
onClick={() => onToggle(id)} //

onClick={onToggle(id)} // 렌더링 시 호출, 함수 호출되면 데이터 변경, 다시 렌더링 무한 반복
```

#### CSS user-select

* 해당요소의 드래그, 더블클릭, 블럭지정을 막는다.

```css
.todo-item {
  user-select: none;
}
```

#### 상태관리

* 컴포넌트끼리 직접적인 데이터 전달하지 않는다.(안티패턴 ref)
* 자식들은 부모를 통해서만 대화한다.
* `App`이 `Form`과 `TodoItemList`의 부모컴포넌트이므로, 해당 컴포넌트에 `input,todos` 상태를 넣어주고 해당 값들과 값들을 업데이트 하는 함수들을 각각 컴포넌트에 `props`로 전달해주어서 기능을 구현한다.

#### Form 기능

* 텍스트 내용 바뀌면 state 업데이트
* 버튼이 클릭되면 새로운 todo 생성 후 todos 업데이트
* 인풋에서 Enter 누르면 버튼을 클릭한것과 동일한 작업진행하기

#### App 컴포넌트 메소드 구현

* `handleChange`
* `handleCreate`
* `handleKeyPress`
* 위 메소드들을 상태의 `input`값과 함께 `Form`컴포넌트로 전달

#### push 사용 NONO

* `push`를 통해 `state` 변경을 하게되면 변경 전과 변경 후 `state`값이 같다.
* 따라서 최적화를 위해 배열 비교하여 리렌더링을 방지해야되는데, `push`를 사용하면 최적화를 하지 못한다.
* `concat`은 새 배열을 만드므로 괜찮다.

#### 비구조화 할당

```js
const { handleChange, handleCreate, handleKeyPress } = this;

// ===

const handleChange = this.handleChange;
```

* `this.handleChange`를 사용하지 않고 `handleChange`로 사용이 가능하다.
* 위 작업을 하지 않을 경우 `this.handleChange`로 작업해도 무방하다.

#### map

* 기본

```js
const todoList = todos.map(todo => (
  <TodoItem
    id={todo.id}
    text={todo.text}
    checked={todo.checked}
    onToggle={onToggle}
    onRemove={onRemove}
    key={todo.id}
  />
));
```

* ES6 비구조화 할당

```js
const todoList = todos.map(({ id, text, checked }) => (
  <TodoItem
    id={id}
    text={text}
    checked={checked}
    key={id}
    onToggle={onToggle}
    onRemove={onRemove}
  />
));
```

* 객체의 값을 모두 props 로 전달하기
* `{...todo}`라고 넣어주면, 내부의 값들이 모두 자동으로 `props`로 설정됩니다.

```js
const todoList = todos.map(todo => (
  <TodoItem {...todo} onToggle={onToggle} onRemove={onRemove} key={todo.id} />
));
```

```js
  todos: [
    { id: 0, text: ' 리액트 소개', checked: false },
    { id: 1, text: ' 리액트 소개2', checked: true },
    { id: 2, text: ' 리액트 소개3', checked: false },
  ],
```

#### onToggle

```js
handleToggle = id => {
  const { todos } = this.state; // 비구조화 할당 this.state.todos를 todos로 편하게 쓰기 위해

  const index = todos.findIndex(todo => todo.id === id); // findIndex 메소드로 현재 클릭된 id값과 todo state id값이 일치하는것을 찾는다.
  const selected = todos[index]; // 일치하는 배열을 찾아 선택

  const nextTodos = [...todos]; // 기존 todos를 복사하여 nextTodos로 할당, 기존 state를 변경하면 안된다.

  nextTodos[index] = {
    ...selected, // 전개연산자(spread) 사용하여
    checked: !selected.checked,
  };

  this.setState({
    todos: nextTodos,
  });
};
```

* 아래와 같이 구현이 가능하지만 너무 복잡하고 가독성이 떨어진다.

```js
handleToggle = id => {
  const { todos } = this.state;
  const index = todos.findIndex(todo => todo.id === id);

  const selected = todos[index];

  this.setState({
    todos: [
      ...todos.slice(0, index),
      {
        ...selected,
        checked: !selected.checked,
      },
      ...todos.slice(index + 1, todos.length),
    ],
  });
};
```

_전개연산자_

* 전개 연산자를 통하여 배열을 복사하는 것은 `deepCopy`가 아닌 `shallowClone`이므로 기존 객체안에 있는것을 재사용하낟. 즉 n 개의 원소가 들어있따면 O(n)정도의 복잡도이다.
* 따라서, 내부의 객체를 바꿔야 할 때는 바꿀 객체를 새로 지정하고 내부의 값을 복사해줘야 합니다.

```js
var select = { id: 0, text: ' 리액트 소개', checked: false };
var e = [];
e[0] = {
  ...select,
  checked: true,
};
//{id: 0, text: " 리액트 소개", checked: true}
e[0] = {
  ...select,
  test: true,
};
//{id: 0, text: " 리액트 소개", checked: false, test: true}
e[0] = {
  ...select,
  text: true,
};
//{id: 0, text: true, checked: false}
```

#### onRemove

```js
handleRemove = id => {
  const { todos } = this.state;
  const nextTodos = todos.filter(todo => todo.id !== id);

  this.setState({
    todos: nextTodos,
  });
};

아래가 더 짧다.

handleRemove = id => {
  const { todos } = this.state;
  this.setState({
    todos: todos.filter(todo => todo.id !== id), // filter 메소드 이용해 id값이 같지 않은 것들만 반환받는다. (!==)
  });
};
```

#### TodoItemList 최적화

* `console.log(id)` 찍어보면 input 에 입력될때마다 `render`함수가 실행되는 걸 볼 수 있다.
* 가상 DOM 과 실제 DOM 을 비교해 변화하지 않았으므로 화면에 반영이 되지 않지만 필요없는 작업이다.

```js
class TodoItem extends Component {
  render() {
    const { text, checked, id, onToggle, onRemove } = this.props;

    console.log(id);

    return ( ...
```

* 컴포넌트 라이프 사이클 메소드중 `shouldComponentUpdate` 사용
* 언제나 `true`를 반환합니다. 조건문을 사용하면 됩니다.
* 현재 `todos`와 변경된 `todos`를 비교해서 진행합니다.

_최적화 작업_

```js
  shouldComponentUpdate(nextProps, nextState) {
    return this.props.todos !== nextProps.todos;
  }
```
