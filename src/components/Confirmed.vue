<script>
export default {
  props: ["orderId", "storeId"], // 從父組件接收 orderId 和 storeId 屬性
  mounted() { // 當組件掛載後執行
    setTimeout(() => {
      // 設置延遲（500毫秒）
      this.$router.push({
        name: "result",
        params: { orderId: this.orderId, storeId: this.storeId } // 傳遞 orderId 和 storeId 作為參數
      });
    }, 500);
  }
};
</script>

<template>
  <div class="container">
    <div class="comfirm_img">
      <img src="../assets/images/confirmed.svg" alt="">
    </div>
  </div>
</template>

<style lang="scss" scoped>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  margin: 0 auto;
  padding: 3%;
  background-color: white;
}

.comfirm_img {
  text-align: center;
}
</style>
