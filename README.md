# 團訂便當系統

## <font size=5>[Demo](https://d22r4nsc4q6o3o.cloudfront.net)<font>

## Login permissions
#### Account : `ccu_order_bento@gmail.com`
#### Password : `a123456`


## Firebase Config File

```
  $ cp src/config.js
```

填入 firebase 的設定

```javascript
export default {
  apiKey: "",
  authDomain: "",
  databaseURL: "",
  projectId: "",
  storageBucket: "",
  messagingSenderId: ""
};

```

## Development Environment
``` bash
1. Windows WSL2 (Ubuntu)
2. Ubuntu 20.04.6 LTS
3. nvm v0.39.5
4. Node.js v12.8.0
```


## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run start

```

## Production setup
``` bash
# build for production with minification
npm run build
```