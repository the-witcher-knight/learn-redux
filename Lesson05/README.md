# Redux Middleware

![One-Way flow with middleware](https://static-assets.codecademy.com/Courses/Learn-Redux/lesson-assets/interactive-image-async-data-flow/interactive-image-files/Data%20Flow-pt2-6.png)

Middleware ở giữa khoảnh khắc một `action` được `dispatch` và khoảnh khắc `action` đó đi qua `reducer`.

Thường dùng Middleware để ghi log, gọi những hàm bất đồng bộ, hoặc bất kì chức năng cần thiết nào không được phép gọi trong `reducer`.

Ví dụ, về middleware ghi log.

```javascript
// dùng applyMiddleware để thêm middleware ngay lúc createStore
import { createStore, applyMiddleware } from "redux";

// reducer
const messageReducer = (state = "", action) => {
  if (action.type === "NEW_MESSAGE") {
    return action.payload;
  } else {
    return state;
  }
};

// middleware ghi log
const logger = (storeAPI) => (next) => (action) => {
  //
  const nextState = next(action);
  console.log(nextState);
  console.log(storeAPI.getState());
  return nextState;
};
const store = createStore(
  messageReducer,
  "",
  applyMiddleware(logger) // thêm middleware
);

store.dispatch({
  type: "NEW_MESSAGE",
  payload: "I WROTE A MIDDLEWARE",
});
```

# Giới thiệt về thunk <a name="thunk"></a>

`thunk` là một hàm bậc cao `higher-order` để kết thúc quá trình tính toán mà chúng ta muốn thực hiện sao này.

Ví dụ, một hàm `thunk`

```javascript
const remindMeTo = (task) => {
  return `Remember to ${task}!!!`;
};

const remindMeLater = (task) => {
  return () => {
    return remindMeTo(task);
  };
};

// hàm remindMeTo sẽ không được thực hiện
// hàm remindMeLater chỉ trả về một hàm sẽ thực hiện khi được gọi
const reminder = remindMeLater("Gi đó");

//
console.log(reminder());
```

# redux-thunk <a name="redux-thunk"></a>

là một middleware để viết các logic bất đồng bộ nó tương tác với `store` bằng cách cho phép viết các `action creator` trả về `thunk` thay vì đối tượng thuần.

Những `thunk` còn có thể thực hiện các hoạt động bất đồng bộ như: dùng để delay khi gửi đi một action (ví dụ, delay cho đến khi nhận được một phản hồi API), hoặc `dispatch` một `action` chỉ khi đủ các điều kiện nhất định.

Ví dụ:

Với `action creator` như sau thì khi `action` được gửi đi thì `count` được tăng ngay lập tức.

```javascript
// action create increment
const increment = () => {
  return {
    type: "counter/increment",
  };
};
```

Với `redux-thunk` ta có thể tạo các `action creator` bất đồng bộ như `asyncIncrement`.

```javascript
const incrementLater = async () => {
  setTimeout(() => {
    dispatch(increment());
  }, 1000);
};

const asyncIncrement = () => {
  return incrementLater;
};
```

> `redux-thunk` đã được cấu hình sẳn trong `redux-toolkit`

# Viết thunk trong Redux <a name="write-thunk-in-redux"></a>

Bình thường `action creator` trả về một đối tượng thuần nhưng với `redux-thunk` nó có thể trả về một `đối tượng thunk` và bên trong đó ta có thể sử dụng bất kì hoạt động bất đồng bộ nào mà chúng ta thích.

Ví dụ:

```javascript
const loadRecipes = () => {
  return async (dispatch, getState) => {
    const payload = await fetchRecipes();
    dispatch({
      type: "allRecipes/addRecipes",
      payload: payload,
    });
  };
};
```

# `redux-thunk` source code <a name='source-code'></a>

Cách làm việc của `redux-thunk` là kiểm tra xem các đối số được chuyển tới `dispatch`. Nếu `dispatch` nhận **_một hàm_** thì `middleware` sẽ gọi nó, nếu nhận một đối tượng thuần thì nó sẽ được pass vào `reducer`.

Source code:

```javascript
function createThunkMiddleware(extraArgument) {
  return ({ dispatch, getState }) =>
    (next) =>
    (action) => {
      if (typeof action === "function") {
        return action(dispatch, getState, extraArgument);
      }

      return next(action);
    };
}

const thunk = createThunkMiddleware();
thunk.withExtraArgument = createThunkMiddleware;

export default thunk;
```
