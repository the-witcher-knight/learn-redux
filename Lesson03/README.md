# React-Redux library

# Tổng quan

Redux one-way data flow

1. Bắt đầu với application state

2. Render các thành phần cơ bản với state

3. Dispatch một action để kích hoạt thay đổi state

4. Render lại các components bị ảnh hưởng bởi state mới

## Lí do sử dụng react-redux lib 

Nếu không sử dụng:

- Phải truyền `state` và `dispatch` thông qua `props`.

- Thành phần `farents` phải định nghĩa các hàm chỉ để truyền cho `child components`, khó tái sử dụng (props drilling).

- Dùng `ReactDOM.render()` nhiều lần là không cần thiết.

Sử dụng react-redux lib:

- Cho toàn ứng dụng truy cập đến `redux store` mà không cần dùng `props` hay `props drilling`.

- Subscribe các `components riêng lẻ` với các `phần chuyên biệt trong state` để tối ưu việc render.

- Dispatch action trong component dễ hơn.

## Cách cài react-redux

```bash
npm i -S react-redux
```

# Provider component <a name='provider_component'></a>

`Provider component` cho các components của ứng dụng truy cập tới `Redux store` mà không cần truyền qua `props`.

Chỉ cần bao `<App />` bằng `<Provider store={store}>` là ok.

```javascript
ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```

# Selector <a name='selector'></a>

Là một hàm thuần trích xuất một phần data cần thiết từ Redux store 's state với cách đặt tên `selectData`.

Ví dụ:

```javascript
/* 
Given a state with an array of objects, labeled 'todos', and a string, labeled 'filter':
 
state = {
  todos: [
    {id: 1, isComplete: true, text: 'Go shopping'}
    {id: 2, isComplete: false, text: 'Call home'}  
  ],
  filter: 'SHOW_ALL'
}
*/
 
// Select the current filter
export const selectFilter = state => state.filter;
 
// Select the `text` from each todo in an array.
export const selectTodoText = state => state.todos.map(
  todo => todo.text);
 
// Select the id values of completed todos in an array.
export const selectIsCompleteIDs = state => state.todos.filter(
  todo => todo.isComplete).map(todo => todo.id)
```

# `useSelector()` Hook <a name='useSelector'></a>

Quy trình 3 bước truy xuất dữ liệu từ `Redux store`:

- **Component** `Provider` cung cấp `store` tới các thành phần bên trong.

- **Selector** tạo ra cách để truy xuất data cần thiết từ `store`.

- **useSelector()** được gọi khi component - (được bọc bên trong component `Provider`) để truy xuất data và `subscribe` re-rendering. 

Cách sử dụng:

```javascript
import { useSelector } from 'react-redux';
import { selectTodos } from 'todosSlice.js';
 
export const Todos = () => {
  const todos = useSelector(selectTodos);
 
  return (
    <p>{todos}</p>
  )
};
```

useSelector cũng có thể dùng selector inline

```javascript
const todos = useSelector((state) => state.todos);
```

# `useDispatch()` Hook <a name='useDispatch'></a>

để có thể `dispatch` một `action` ở bất kì component nào - (được bao bởi `Provider`) ta dùng `useDispatch` hook.

Ví dụ: 
```javascript
const dispatch = useDispatch();
```

# Tổng kết 

`react-redux` library cho các components trong ứng dụng react kết nối tới redux store một cách dễ dàng:

- components `<Provider>` bao xung quanh root component để các component bên trong có thể access tới `redux store` mà không cần dùng `props drilling`.

- `Selector` là một hàm thuần được sử dụng để truy cập tới một phần hoặc toàn bộ state trong Redux store.

- `useSelector` lấy `state` thông qua selector. Nó phải được gọi trong component.

- `useSelector` *subscribe* components để data lấy về từ selector. React sẽ re-renders các component đó khi `selected data` thay đổi.

- `useDispatch` trả về tham chiếu của `Redux store dispatch`.
