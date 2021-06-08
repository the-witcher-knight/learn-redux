# Cài đặt thư viện Redux

```bash
npm install redux
```

# Create a Redux Store

Store là một đối tượng thực thi nguyên lý luồng dữ liệu 1 chiều mà Redux dự trên nó. Nó giữ `current state` nhận các `Action được gửi đi`, thực thi những `reducer` để tạo ra `state mới` và cung cấp khả năng truy cập vào `state` để UI hiển thị ra.

`import { createStore } from 'redux'` và sửa dụng `createStore` để tạo store  

ví dụ:

```javascript
import { createStore } from 'redux';

const initialState = 0; // biến lưu giá trị mặc định của state 
// Tạo reducer count
const countReducer = (state = initialState, action) => {
  switch(action.type){
    case 'increment':
      return state + 1;
    case 'decrement':
      return state - 1;
    default:
      return state;
  }
}
// tạo store
const store = createStore(countReducer);
```

# Dispatch Action to the Store

Đối tượng `store` được trả về bởi method `createStore` như trên sẽ cung cấp một số function giúp tương tác với `state`.

`store.dispatch()` được sử dụng để gửi đi một `action` ...làm thay đổi `state` với reducer có `action.type`.

ví dụ:

```javascript
...ví dụ trên

store.dispatch({type: 'increment'});
store.dispatch({type: 'decrement'});
console.log(store.getState());// log ra số 0
```

# Respond to State Change

Trong Redux, các Action được gửi đến store có thể được lắng nghe - `listener` và trả lời - `Respond` bằng cách sử dụng `store.subscribe()`. Đối số của hàm này là một function được gọi là `listener`, nó sẽ thực thi các đáp ứng được thay đổi với các `state` trong store.

ví dụ: 

```javascript
... ví dụ trên
const printCountStatus = () => {
  console.log(`count: ${store.getState()}`);
}

store.subscribe(printCountStatus);
```

Như vậy mỗi lần làm thay đổi `state` ứng dụng sẽ tự động log ra tham số `count`.

# React Redux

Một ví dụ cơ bản việc sử dụng react với redux: khi user ấn vào 1 `button` sẽ gửi đi - `dispatch` một `action` làm tăng/giảm count trong state, `store.subscribe()` sẽ lắng nghe sự thay đổi của state và gọi hàm `render()` để re-render lại UI với state mới.

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import { createStore } from 'redux';

// REDUX CODE
///////////////////////////////////

const increment = () => {
  return {type: 'increment'} 
}

const decrement = () => { 
  return {type: 'decrement'}
}

const initialState = 0;
const counterReducer = (state = initialState, action) => {
  switch (action.type) {
    case 'increment':
      return state + 1;
    case 'decrement':
      return state - 1;
    default:
      return state; 
  }
} 

const store = createStore(counterReducer);

// REACT CODE
///////////////////////////////////

const render = () => {
  ReactDOM.render(
    <CounterApp 
      state={store.getState()}
    />,
    document.getElementById('root')
  )
}


// Render once with the initial state.
render()
// Subscribe render to changes to the store's state.
store.subscribe(render);

function CounterApp(props) {

  const state = props.state;
  
  const onIncrementButtonClicked = () => {
    // Dispatch an 'increment' action.
    store.dispatch(increment());
  }
 
  const onDecrementButtonClicked = () => {
    // Dispatch an 'decrement' action.
    store.dispatch(decrement());
  }
  
  return (   
    <div id='counter-app'>
      <h1> {state} </h1>
      <button onClick={onIncrementButtonClicked}>+</button> 
      <button onClick={onDecrementButtonClicked}>-</button>
    </div>
  )
}
```
