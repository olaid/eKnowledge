# JAMのReduxについて

# client/src/modules/index.ts

```tsx
const store = createStore(reducer, enhancer);
```

上記によりstoreは生成されます。storeはアプリ全体で持っている一つのまとまった変数の塊のようなものです。

client/src/modules/account.tsにあるReducerのaccountReducerを読み込んでいるのでこれに沿った状態(store)の遷移(action)を行います。

# client/src/modules/account.ts

```tsx
export const accountActions = {
    updateId: actionCreator<number>('ACTIONS_UPDATE_ID'),
    updateName: actionCreator<string>('ACTIONS_UPDATE_NAME'),
    updateEmail: actionCreator<string>('ACTIONS_UPDATE_EMAIL'),
}
```

上記によりaccountActionsモジュールが定義されます。

モジュールは公開され他のファイルからも利用ができます。

accountActions.updateEmailはactionCreatorでdispatchすることでActionを生成します。

アプリ全体の状態(store)を変更(action)します。

```tsx
// アカウントデータ Reducer
export const accountReducer = reducerWithInitialState(initialAccountState)
.case(accountActions.updateId, (state, id) => {
return Object.assign({}, state, { id });
})
.case(accountActions.updateName, (state, name) => {
return Object.assign({}, state, { name });
})
.case(accountActions.updateEmail, (state, email) => {
return Object.assign({}, state, { email });
})
```

上記によりaccountReducerモジュールが定義されます。

accountReducerはReducerでstoreの各状態を定義します。

# client/src/containers/loginContainer.ts

```tsx
import { accountActions, authActions, accountModulesActions } from '../modules/account';
```

上記によりaccount.tsを読み込んでいます。

client/src/containers/navigationContainer.tsも同様でaccount.tsを読み込んでいます。

```tsx
updateEmail: (v: string) => dispatch(accountActions.updateEmail(v)),
```

上記によりaccount.ts定義されたモジュールを呼び出します。