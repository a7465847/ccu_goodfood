<script>
import FirebaseManager from "@/utils/FirebaseManager"; // 引入 Firebase 管理模塊

export default {
  props: ["user", "storeId", "orderId", "uid"], // 從父組件接收用戶、商店ID、訂單ID和用戶ID
  data() {
    return {
      total: 0, // 總金額
      comfirmed: false // 是否確認
    };
  },
  mounted() {
    // 當組件掛載後，從 Firebase 讀取並設置訂單總金額
    FirebaseManager.database
      .ref("order/" + this.orderId + "/result")
      .child("total")
      .once("value")
      .then(snapshot => {
        this.total = snapshot.val();
        console.log(this.total);
      });
  },

  methods: {
    comfirmOrder() {
      // 確認訂單方法
      // 更新 Firebase 中的訂單總金額
      // 先獲取資料庫的總訂單金額，加上此次訂單金額，再更新上去
      FirebaseManager.database
        .ref("order/" + this.orderId + "/result")
        .child("total")
        .once("value")
        .then(snapshot => {
          this.total = snapshot.val();
          console.log(this.total);
          this.total += this.user.total; // 增加用戶的訂單金額
          console.log(this.total);
        })
        .then(() => {
          FirebaseManager.database
            .ref("order/" + this.orderId + "/result")
            .child("total")
            .set(this.total); // 更新 Firebase 中的總金額
        });

      console.log(this.user);
      // 更新用戶訂單信息
      let update = {};
      update[this.uid] = this.user;
      FirebaseManager.database
        .ref("order/" + this.orderId + "/result/users")
        .update(update)
        .then(data => {
          console.log(data);
          // 導航到確認頁面
          this.$router.push({
            name: "confirmed",
            params: { storeId: this.storeId, orderId: this.orderId }
          });
        });
    },
    cancelOrder() {
      this.$emit("cancelOrder");
      this.$router.go(-1); // 返回上一頁
    }
  }
};
</script>

<template>
<div class="comfirm_item">
  <h1 class="comfirm_title">{{user.name}}的訂單</h1>
  <ul>
    <li class="order_detail" v-for="(dish,id) in user.order" :key="id">
      <div class="flex">
        <div>{{dish.name}}</div>
        <div>{{dish.count}}份</div>
      </div>
      <div class="comfirm_subtotal">${{dish.total}}</div>

    </li>
  </ul>
  <div class="comfirm_total">
    <span>總共</span>
    ＄{{user.total}}</div>
  <div class="btn_group">
    <a href="#" class="cancel_btn" @click="cancelOrder">修改訂單</a>
    <a href="#" class="comfirm_btn" @click="comfirmOrder">確認訂單</a>
  </div>

</div>
</template>

<style lang="scss" scoped>
@import "../scss/index.scss";

li {
  list-style: none;
}

a {
  color: $orange;
  text-decoration: none;
}

.comfirm_item {
  width: 90%;
  font-size: 14px;
  margin: 0 auto;
  padding-top: 60px;
  padding-bottom: 60px;
}

.comfirm_title {
  margin-bottom: 24px;
  font-size: 17px;
  text-align: center;
}

.comfirm_item {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: white;
}

.flex {
  display: flex;
  justify-content: space-between;
}

.flex,
.comfirm_subtotal {
  padding: 6px 6px 6px 0;
}

.order_detail {
  padding: 10px;
  margin: 10px 0;
  border-radius: 15px;
  background-color: $gray_one;
}

.comfirm_subtotal {
  text-align: right;
}

.comfirm_total {
  text-align: right;
  span {
    color: $blue;
  }
}

.comfirm_btn,
.cancel_btn {
  display: block;
  margin: 0 6px;
  width: 130px;
  height: 50px;
  line-height: 50px;
  text-align: center;
  border: 1px $orange solid;
  border-radius: 30px;
}

.btn_group {
  display: flex;
  justify-content: space-around;
  padding: 50px 0;
}
</style>
