# Slices 

là một thuộc tính nào đó trong đối tượng `state`, có thể là array, string, number, boolean, object. Ví dụ:
```javascript
state = {
  allRecipes: [
    {id: 0, name: 'Jjampong', img: 'img/jjampong.png' },
    {id: 2, name: 'Cheeseburger', img: 'img/cheeseburger.png' },
    //… more recipes omitted
  ],
  favoriteRecipes: [
    {id: 1, name: 'Doro Wat', img: 'img/doro-wat.png' },
  ],
  searchTerm: 'Doro'
};
```

trong ví dụ trên có 3 `slice` là `allRecipes`, `favoriteRecipes` và `searchTerm`.

> Chú ý rằng nên tạo `initialState` để định hình cấu trúc của `state` và khởi tạo giá trị mặc định để `reducer` xử lý.

ví dụ `initialState` cho state trên là:

```javascript
const initialState = {
  allRecipes: [],
  favoriteRecipes: [],
  searchTerm: ''
}
```

# Action và Payload trong state phức tạp 

Khi một `state` có nhiều `slice` thì ta nên đặt `action.type` có dạng như `sliceName/actionDescrition` để dễ nhận biết `action` nào của `slice` nào.

Cần suy nghĩ một số ý tưởng về `action` phù hợp với UI. Và dùng `action creator` để tạo ra những action thích hợp có thể tái sử dụng

> chú ý, action create là một hàm trả về 1 đối tượng được có định dạng nhất định {type: string, payload?: any}

# Immutable Update & Complex State

Tương tự như với state đơn giản, với state phức tạp vẫn tuân thủ 3 rule of reducer. Vì `state phức tạp` là một `đối tượng` nên ta dùng `{...state, key: new value}`.
Ví dụ như sau:

```javascript
import { createStore } from 'redux';
import allRecipesData from './data.js';

const initialState = {
  allRecipes: [],
  favoriteRecipes: [],
  searchTerm: ''
};

const setSearchTerm = (term) => {
  return {
    type: 'searchTerm/setSearchTerm',
    payload: term
  };
}

const clearSearchTerm = () => {
  return {
    type: 'searchTerm/clearSearchTerm'
  }; 
};

const loadData = () => {
  return { 
    type: 'allRecipes/loadData', 
    payload: allRecipesData
  };
};

const addRecipe = (recipe) => {
  return { 
    type: 'favoriteRecipes/addRecipe', 
    payload: recipe 
  };
};

const removeRecipe = (recipe) => {
  return { 
    type: 'favoriteRecipes/removeRecipe', 
    payload: recipe 
  };
};

/* Complete this reducer */
const recipesReducer = (state = initialState, action) => {
  switch(action.type) {
    case 'allRecipes/loadData':
      return { 
        ...state,
        allRecipes: action.payload
      }
    case 'searchTerm/clearSearchTerm':
      return {
        ...state,
        searchTerm: ''
      }
    
    case 'searchTerm/setSearchTerm':
      return {
        ...state,
        searchTerm: action.payload,
      }

    case 'favoriteRecipes/addRecipe':
      return {
        ...state,
        favoriteRecipes: [...state.favoriteRecipes, action.payload]
      }

    case 'favoriteRecipes/removeRecipe':
      return {
        ...state,
        favoriteRecipes: state.favoriteRecipes.filter(
            recipe => recipe.id != action.payload.id
          )
      }

    default:
      return state;
  }
};

const store = createStore(recipesReducer);
```

# Reducer Composition

Đối với những ứng dụng lớn nên tạo reducer riêng `sliceReducer` cho từng slice trong state để dễ quản lý. Sau cùng, được gọi trong một `rootReducer`.

Và `initialState` cũng nên được chia thành những `initialSlice` tương ứng với mỗi slice.

Cập nhật ví dụ ở trên:

```javascript
const initialAllRecipes = [];
const allRecipesReducer = (allRecipes = initialAllRecipes, action) => {
  switch(action.type){
    case 'allRecipes/loadData':
      return action.payload;
    default:
      return allRecipes;
  }
}

const initialSearchTerm = '';
const searchTermReducer = (searchTerm = initialSearchTerm, action) => {
  switch(action.type){
    case 'searchTerm/setSearchTerm':
      return action.payload;
    case 'searchTerm/clearSearchTerm':
      return '';
    default:
      return searchTerm;
  }
}

const initialFavoriteRecipes = [];
const favoriteRecipesReducer = (favoriteRecipes = initialFavoriteRecipes, action) => {
  switch(action.type){
    case 'favoriteRecipes/addRecipe':
      return [...favoriteRecipes, action.payload];
    case 'favoriteRecipes/removeRecipe':
      return favoriteRecipes.filter(recipe => recipe.id != action.payload.id);
    default:
      return favoriteRecipes;
  }
}

// Tạo rootReducer
const rootReducer = (state = {}, action) => {
  const state = {
    allRecipes: allRecipesReducer(state.allRecipes, action),
    favoriteRecipes: favoriteRecipesReducer(state.favoriteRecipes, action),
    searchTerm: searchTermReducer(state.searchTerm, action)
  };

  return state;
}

const store = createStore(rootReducer);
```

# Combine Reducers

Như ví dụ trên, ta đã lưu trữ các `slice reducer` vào 1 đối tượng và trả về bởi hàm `rootReducer()`. 

Tuy nhiên, Redux cung cấp `combineReducers()` để làm việc này thay ta.

```javascript
const reducers = {
  allRecipes: allRecipesReducer,
  favoriteRecipes: favoriteRecipesReducer,
  searchTerm: searchTermReducer
}

const rootReducer = combineReducers(reducers);

const store = createStore(rootReducer);
```

hoặc ta có thể viết ngắn gọn:

```javascript
const store = createStore(combineReducers({
  allRecipes: allRecipesReducer,
  favoriteRecipes: favoriteRecipesReducer,
  searchTerm: searchTermReducer,
}))
```

