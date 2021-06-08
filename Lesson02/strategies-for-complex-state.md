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

/* DO NOT DELETE */
printTests();
function printTests() {
  store.dispatch(loadData());
  console.log('Initial State after loading data');
  console.log(store.getState());
  console.log();
  store.dispatch(addRecipe(allRecipesData[0]));
  store.dispatch(addRecipe(allRecipesData[1]));
  store.dispatch(setSearchTerm('cheese'));
  console.log("After favoriting Biscuits and Bulgogi and setting the search term to 'cheese'")
  console.log(store.getState());
  console.log();
  store.dispatch(removeRecipe(allRecipesData[1]));
  store.dispatch(clearSearchTerm());
  console.log("After un-favoriting Bulgogi and clearing the search term:")
  console.log(store.getState());
}
```