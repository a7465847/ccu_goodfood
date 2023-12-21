<template>
  <div class="container">
    <div class="confirm-new-add-menu" :style="{backgroundImage:`url(${img})`}">
    </div>
  </div>
</template>
<script>
export default {
  props: ["storeId"],
  data() {
    return {
      img: require("@/assets/images/order-icon.svg") // 從 assets 中加載圖像並賦值給 img 變量
    };
  },
  mounted() {
    // 當組件掛載後執行
    // 設置延遲（2500毫秒）
    setTimeout(() => {
      this.$router.push({
        name: "addmenu",
        params: { storeId: this.storeId }
      });
    }, 2500);
  }
};
</script>
<style lang="scss" scoped>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
}

.confirm-new-add-menu {
  height: 205px;
  width: 205px;
}
</style>

