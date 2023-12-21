<script>
import FirebaseManager from "@/utils/FirebaseManager"; // 引入 Firebase 管理模塊
import olddata from "@/goodfoodOld"; // 引入舊數據

let oldMenu = olddata.dish; // 獲取舊菜單數據

async function uploadStore(store) {
  // 異步函數上傳店家信息並返回鍵值
  let storeKey = await FirebaseManager.database.ref("store").push(store).key;
  console.log(store.name, storeKey);
  return storeKey;
}

// 異步函數上傳菜單信息並返回鍵值
async function uploadMenu(storeKey, menu, store) {
  let menuKey = await FirebaseManager.database
    .ref("store")
    .child(storeKey)
    .child("menus")
    .push(menu).key;
  console.log(store.name, menu.name, menuKey);

  return menuKey;
}

export default {
  data() {
    return {
      route: "", // Firebase 數據路徑
      updateData: "", // 要更新的數據
      msg: "" // 信息顯示
    };
  },
  methods: {
    updateStore() {
      // 更新所有店家信息
      let oldStores = olddata.store;

      for (let oldKey in oldStores) {
        let oldStore = oldStores[oldKey];
        let store = {};
        store.name = oldStore.storeName;
        store.address = oldStore.address;

        store.orderIn = {};
        store.orderIn.unit = "";
        store.orderIn.count = "";

        store.time = {};
        store.time.start = oldStore.openTime;
        store.time.end = oldStore.closeTime;

        store.tel = {};
        let tel = oldStore.phone.split("-");
        store.tel.block = tel[0];
        store.tel.num = tel[1];

        store.mark = oldStore.memo || "";

        uploadStore(store).then(storeKey => {
          for (let dishId in oldMenu) {
            if (oldMenu[dishId].storeId === oldKey) {
              let menu = {};
              menu.name = oldMenu[dishId].dishName;
              menu.price = oldMenu[dishId].price;

              menu.options = [
                {
                  max: 0,
                  min: 0,
                  chooses: [
                    {
                      name: ""
                    }
                  ]
                }
              ];
              uploadMenu(storeKey, menu, store);
            }
          }
        });
      }
    },
    update() {
      this.msg = ""; // 更新特定路徑的數據

      FirebaseManager.database
        .ref(this.route)
        .set(this.updateData)
        .then(() => {
          this.msg = "修改成功";
        });
    }
  }
};
</script>

<template>
  <div>
    <!-- <button @click="updateStore">updateAllStore</button> -->
    <label for="route">路徑：</label><input id="route" type="text" v-model="route"><br>
    <label for="updateData">修改內容：</label><input id="updateData" type="text" v-model="updateData">
    <button @click="update">修改某一筆</button>
    <div>{{msg}}</div>
    
  </div>
</template>
