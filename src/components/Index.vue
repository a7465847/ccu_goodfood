<script>
import FirebaseManager from "@/utils/FirebaseManager"; // 引入 Firebase 管理模塊
import checkAuth from "@/checkAuth"; // 引入用戶驗證模塊
import "swiper/dist/css/swiper.css"; // 引入 Swiper 的 CSS
import "../scss/swiper-style.css"; // 引入自定義的 Swiper 樣式
import { swiper, swiperSlide } from "vue-awesome-swiper"; // 引入 Swiper 插件的組件
// import lodashfp from "lodash/fp"; 

export default {
  data() {
    return {
      uid: "",
      displayName: "Guest",
      lottery: "", // 抽選獲得的店家
      recommend: [], // 推薦的店家數組
      swiperOption: { // Swiper 的配置選項
        slidesPerView: 2.2,
        spaceBetween: 10,
        freeMode: true,
        grabCursor: true,
        pagination: {
          el: ".swiper-pagination",
          bulletActiveClass: "index-bullet-active"
        },
        navigation: {
          nextEl: ".swiper-button-next",
          prevEl: ".swiper-button-prev"
        }
        // autoplay: false,
        // loop: true
      }
    };
  },
  components: {
    swiper,
    swiperSlide // 註冊 Swiper 組件
  },
  created() {
    // 從 Firebase 獲取並設置推薦店家
    FirebaseManager.database
      .ref("store")
      .once("value")
      .then(snapshot => {
        var stores = [];
        var store = {};
        snapshot.forEach(data => {
          store = data.val();
          store.key = data.key;
          stores.push(store);
        });
        var recommendNum = 5;
        var recommend = randomStore(stores, recommendNum);
        this.recommend = recommend;
        console.log(recommend);

        // 抽出好手氣店家
        var lottery = randomStore(stores, 1);
        console.log(lottery);
        this.lottery = lottery.pop();
      });

    // 隨機抽出n個不重複的項目
    function randomStore(rawData, count) {
      var result = [];
      var data = rawData.slice(0);
      for (var i = 0; i < count; i++) {
        var ran = Math.floor(Math.random() * data.length);
        result.push(data.splice(ran, 1)[0]);
      }
      return result;
    }
    // 用戶驗證
    checkAuth.checkAuth().then(userInfo => {
      this.uid = userInfo.uid;
      this.displayName = userInfo.displayName;
    });
  },
  methods: {
    signOut() {
      FirebaseManager.signOut().then(function() {
        // Sign-out successful.
      })
        .catch(function(error) {
        // An error happened.
          console.log(error);
        });
      this.$router.push("/login");
    }
  }
};
</script>

<template>
  <div class="container">
    <span class="hello">Hi, {{displayName}}</span>
    <a class="signout" @click="signOut">登出</a>
    <div class="wrapper">
      <img src="../assets/images/logo.svg" alt="">
      <div class="logo_title">SET <br>LUNCH</div>
      <div class="lottery_wrapper">
          <router-link class="lottery btn" :to="{name:'storeinfo', params:{storeId:this.lottery.key}}">
            <img class="icon_lottery" src="../assets/images/icon_lottery.svg" alt="">
            好手氣
          </router-link>
          <router-link class="storelist btn" :to="{name:'storelist'}">
            <img class="icon_storelist" src="../assets/images/icon_storelist.svg" alt="">
            看所有店家
          </router-link>
      
      </div>
      
     
    </div>
    <div class="recommend_title">
      <img class="icon_storelist" src="../assets/images/icon_recommend.svg" alt="">
      今日推薦
    </div>

    <swiper :options="swiperOption" class="mt-4 mb-5 recommend_wrapper">
      <swiperSlide class="recommend" v-for="(store,idx) in recommend" :key="idx">
          <router-link class="store" :to="{name:'storeinfo', params:{storeId:store.key}}">
          
          <img class="cover_img" :src="store.mark || '../assets/images/cover3.jpg'" alt="">
          <div class="store_name">{{store.name}}</div>
            <!-- {{store.name}} -->
          </router-link>
      </swiperSlide>
      <div class="swiper-pagination" slot="pagination"></div>
      <div class="swiper-button-prev swiper-button-white" slot="button-prev"></div>
      <div class="swiper-button-next swiper-button-white" slot="button-next"></div>
    </swiper>
          <router-link class="addstore_btn" :to="{name:'addstore'}">新增店家</router-link>
    
  </div>
</template>


<style lang="scss" scoped>
@import "../scss/index.scss";
a {
  color: $orange;
  text-decoration: none;
}

.signout {
  position: absolute;
  display: inline-block;
  right: 0;
  font-size: 13px;
  cursor: pointer;
}
.container {
  position: relative;
  width: 375px;
  margin: 5px auto;
  padding: 0 15px;

  .hello {
    font-size: 12px;
    color: $font_primry;
  }
}
.wrapper {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding-top: 80px;

  .logo_title {
    font-size: 16px;
    font-family: Arial, Helvetica, sans-serif;
    font-weight: bold;
    color: #4a4a4a;
    text-align: center;
    padding-top: 16px;
    padding-bottom: 40px;
  }

  .lottery_wrapper {
    display: flex;
    padding-bottom: 27px;
    border-bottom: solid 1px $white;

    .lottery {
      margin-right: 9px;
    }
    .btn {
      width: 150px;
      height: 46px;

      line-height: 46px;
      font-size: 14px;
      display: block;
      text-align: center;
      border: 1px $orange solid;
      border-radius: 30px;

      img {
        vertical-align: middle;
      }
    }
  }
}

.recommend_title {
  padding: 14px 0 19px 23px;

  img {
    vertical-align: middle;
  }
}

.recommend_wrapper {
  height: 215px;

  .recommend {
    /* width: 200px !important;
  height: 100px; */
    width: 154px;
    height: 173px;
    // padding: 0 6px;
    border-radius: 8px;
    overflow: hidden;
    box-shadow: 0px 5px 7px 3px rgba(0, 0, 0, 0.15);
    border-bottom: white solid 3px;
    box-sizing: border-box;

    .store {
      display: block;
    }
    .cover_img {
      width: 100%;
      height: 134px;
      object-fit: contain;
    }

    .store_name {
      display: block;
      font-size: 13px;
      line-height: 38px;
      text-align: center;
      color: $font_primry;
    }
  }
}

.addstore_btn {
  display: block;
  width: 340px;
  height: 40px;
  margin: 0 auto;
  line-height: 40px;
  text-align: center;
  color: white;

  border-radius: 21px;
  background: #75bafa;
}
</style>
