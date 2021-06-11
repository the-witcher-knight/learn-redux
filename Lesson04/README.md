# Redux Toolkit

# Tổng quan

## Giới thiệu

Redux còn tồn tại nhiều vấn đề:

- Cầu hình store còn quá phức tạp

- Cần phải cài thêm nhiều package để redux hữu dụng

- Redux cần nhiều boilerplate code

- Viết các hàm Immutable Update dễ gây ra lỗi.

Vì vậy, cần dùng `redux-toolkit`

## Cách cài

```bash
npm i -S @reduxjs/toolkit
```

# Refactoring with createSlice() <a name='createSlice'></a>

Nhớ lại `slice` là một thuộc tính _kiểu đối tượng_ trong một đối trượng `state`.

Ưu điểm khi sử dụng `createSlice()`:

- Viết các trường hợp của `reducer` bên trong một đối tượng thay vì dùng _switch_

- Các `Action Creator` sẽ được tạo tự động.

- Không cần trả về kết quả mặc định trong `reducer`.

# Viết "Mutable" code với thư viện Immer <a name="mutable"></a>

Khi sử dụng `createSlice()` thì không cần phải tuân thủ quy tắc `Immutable Update` của reducer.

Immer sử dụng một đổi tượng JS đặc biệt là `Proxy` để bọc data. Immer tracking toàn bộ thay đổi state và sau đó trả về một giá `Immutable Update` từ list thay đổi đó. Tuy nhiên thích việt Immutable Update thì vẫn được.

Ví dụ:

```javascript
reducers: {
  addTodo: (state, action) => {
    return [
      ...state,
      {
        ...action.payload,
        completed: false
      }
    ]
  },
  toggleTodo: (state, action) => {
    return state.map(todo =>
      todo.id === action.payload.id ? { ...todo, completed: !todo.completed } : todo
    )
  }
}
```

có thể viết như thế này

```javascript
reducers: {
  addTodo: (state, action) => {
    state.push({
      ...action.payload,
      completed: false
    })
  },
  toggleTodo: (state, action) => {
    const todo = state.find(todo => todo.id === action.payload.id)
    if (todo) {
      todo.completed = !todo.completed
    }
  }
}
```

# Return Object <a name="return_object"></a>

Hàm `createSlice()` trả về đối tượng có dạng:

```bash
{
  name: string,
  reducer: (state, action) => newState,
  actions: {
    addTodo: (payload) => ({type: 'todos/addTodo', payload}),
    ...
  }
}
```

## Action <a name="return_action"></a>

để lấy những Action ra:

```javascript
const { addTodo, removeTodo, toggleTodo } = nameSlice.actions;
```

## Reducer <a name="return_reducer"></a>

```javascript
const todoReducer = nameSlice.reducer;
```

# Sử dụng `configureStore()` thay `createStore()` <a name="configureStore"></a>

Thay vì

```javascript
const rootReducer = {
  allRecipe: allReciperReducer,
  searchTerm: searchTermReducer,
  favoriteRecipeReducer: favoriteRecipeReducer,
};

export default createStore(combineReducer(rootReducer));
```

với `redux toolkit` ta dùng:

```javascript
export default const store = configureStore({
  reducer: {
    allRecipe: allReciperReducer,
    searchTerm: searchTermReducer,
    favoriteRecipeReducer: favoriteRecipeReducer
  }
})
```

`configureStore()` sẽ :

- _combine_ `allRecipeReducer`, `searchTermReducer` và `favoriteRecipeReducer` vào hàm `rootReducer`, handle `rootState` => `{allRecipe, searchTerm, favoriteRecipe}` mà không cần dùng `combineReducer`.

- nó tạo `Redux store` với `rootReducer` mà không cần dùng `createStore()`.

- nó từ đông thêm `thunk middleware`.

- nó tự động thêm nhiều `middleware` để kiểm tra những sai lầm chung làm thay đổi trạng thái.

- nó tự động set up kết nối với Redux DevTools.
