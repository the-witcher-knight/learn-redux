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
