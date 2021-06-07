# Core Concept in Redux

# State <a name='state'></a>

State là thông tin hiện tại đằng sau một ứng dụng web

Với calendar app nó có thể là các sự kiện (tên, ngày, label,...). Với Todo app nó có thể là các todo item (mô tả, complete/not complete,...) filter display.

Với Redux, state có thể là bất kì kiểu dữ liệu JS nào, bao gồm: string, number, array, boolean, object. 

Một ví dụ cho state `const state = ['a', 'b', 'c', 'd', 'e', 'f']`

# Actions <a name='actions'></a>

Một ứng dụng Todo list có thể có 1 input field để user nhập todo item. Ứng dụng sẽ chuyển data từ input field đó vào array list và hiển thị ra màn hình. Toàn bộ những tương tác đó được gọi là Action. Trong Redux, Action là một plain JS object (đối tượng JS thuần túy)

Một ví dụ về action

```javascript
const action {
  type: 'todo/addTodo',
  payload: 'To do mới',
}
```

- Mọi action đều phải có `type` để cho biết action đó là gì.
- Thông thường `Action` sẽ có thêm `payload` chứa thông tin liên quan trong action. Ở ví dụ trên payload chứa todo item cần add vào.
- Khi một Action được tạo ra và thông báo đến các component khác của ứng dụng, ta gọi là action được gửi đi - *dispatched*.

Dưới đây là 2 ví dụ về action

```javascript
const action { 
  type: 'todo/removeAll' // xóa toàn bộ todo, không có paylpay vì không có thông tin cần thêm vào
}; 
```

```javascript
const action { 
  type: 'todo/removeOne', // xóa action1
  payload: 'action1'
}
```

# Reducer <a name='reducer'></a> 

Một reducer hoặc hàm reducer là một hàm thuần túy cho biết cách phối hợp giữa current state và action để tạo ra state mới.

Đây là một ví dụ:

```javascript
const initialState = ['a', 'b', 'c', 'd', 'e', 'f'];

const reducer = (state = initialState, action) => {
  switch(action.type){
    case 'todo/add':
      return [...state, action.payload];
    case 'todo/removeAll':
      return [];
    default: 
      return state;
  }
}
```

một vài điều trong hàm reducer này đúng với mọi reducer:
- Đây là một hàm js thuần.
- Nó cho biết trạng thái tiếp theo (next state) của ứng dụng với current state và một action cụ thể.
- Mặc định trả về `initialState` nếu không có action cụ thể.
- Trả về current state nếu action không được định nghĩa trước đó.

# Rule Of Reducers <a name='rule_of_reducer'></a>

1. Chỉ tính toán `state mới` dựa trên `state hiện tại` và `action`.
2. Không được phép chỉnh sửa `state` mà phải copy `state` đó và thực hiện tính toán trên bản copy.
3. Không được phép sửa dụng bất kì hàm "async logic" hoặc "side effect"

với "async logic", "side effect" có thể là `random()`, ghi log, lưu file, hay tạo request HTTP.

# Immutable Updates and Pure Functions <a name='immutable_update_and_pure_function'></a> 

3 quy tắc của reducer có thể hiểu đơn giản là: reducer phải là `immutable update` và `pure function`:

- Một hàm immutable update là hàm không thay đổi giá trị của tham số, mà thay vào đó nó tạo 1 copy và thay đổi copy đó (giống luật 2).

```javascript
// Hàm bình thường
const mutableUpdate = (obj) => {
  obj.completed = !obj.compleobj;
  return obj;
}

// hàm Immutable Update 
const immutableUpdate = (obj) => {
  return {...obj, completed : !obj.completed};
}
```
- Một hàm pure là hàm luôn luôn cho ra output giống nhau với những input giống nhau. Nó là sự phối hợp giữa luật 2,3 - (tạo ra `state mới` dựa trên `current state` và `action`. không được sử dụng bất kì logic bất đồng bộ nào hoặc "side effect").

```javascript
const fs = require('fs');
const file = './data.txt';

const message = fs.readFileSync(file, 'utf8');

const capitalizeMessage = (message) => {
  return message.toUpperCase();
}

console.log(capitalizeMessage(message));
```

# Store <a name='store'></a>

Store hoạt động như 1 kho chứa state, có cung cấp các cách để `dispatch action` - gửi action. Và nó gọi reducer khi `action` được gửi đi. Gần như mọi ứng dụng redux chỉ có 1 store.

![](https://redux.js.org/assets/images/ReduxDataFlowDiagram-49fa8c3968371d9ef6f2a1486bd40a26.gif)

1. store khởi tạo state vơi giá trị mặc đinh.
2. view hiển thị giao diện.
3. khi user tương tác với giao diện như là nhấn nút thì một action sẽ được gửi đến (dispatched) store.
4. `action được gửi đi` và `state hiện tại` sẽ phối hợp với nhau để tạo ra `state mới`.
5. Giao diện sẽ hiển thị `state mới`.


