<template>
  <div>
    <head-top signin-up='msite'>
      <router-link :to="'/search/geohash'" class="link_search" slot="search">
        <svg width="100%" height="100%" xmlns="http://www.w3.org/2000/svg" version="1.1">
          <circle cx="8" cy="8" r="7" stroke="rgb(255,255,255)" stroke-width="1" fill="none"/>
          <line x1="14" y1="14" x2="20" y2="20" style="stroke:rgb(255,255,255);stroke-width:2"/>
        </svg>
      </router-link>
      <router-link to="/home" slot="msite-title" class="msite_title">
        <span class="title_text ellipsis">{{msiteTitle}}</span>
      </router-link>
    </head-top>

    <nav class="msite_nav">
      <div class="area_type_container">
        <router-link to="/vipcard" class="area_type_item">
          <div class="area_type_img">
            <img src="../../images/areaType/area_icon1.png" alt="">
          </div>
          <div class="area_type_text">
            签到
          </div>
        </router-link>
        <router-link to="/statistics" class="area_type_item">
          <div class="area_type_img">
            <img src="../../images/areaType/area_icon2.png" alt="">
          </div>
          <div class="area_type_text">
            数据统计
          </div>
        </router-link>
        <router-link to="/equipment" class="area_type_item">
          <div class="area_type_img">
            <img src="../../images/areaType/area_icon3.png" alt="">
          </div>
          <div class="area_type_text">
            设备管理
          </div>
        </router-link>
        <router-link to="/alarm" class="area_type_item">
          <div class="area_type_img">
            <img src="../../images/areaType/area_icon4.png" alt="">
          </div>
          <div class="area_type_text">
            预警设置
          </div>
        </router-link>
        <router-link to="/plant" class="area_type_item">
          <div class="area_type_img">
            <img src="../../images/areaType/area_icon5.png" alt="">
          </div>
          <div class="area_type_text">
            种植管理
          </div>
        </router-link>

        <div class="swiper-pagination"></div>
      </div>
    </nav>

    <div class="area_list_container">
      <header class="area_header">
        <svg class="area_icon">
          <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#shop"></use>
        </svg>
        <span class="area_header_title">区域管理</span>
      </header>
      <area-list v-if="hasGetData" :geohash="geohash"></area-list>
    </div>

    <foot-guide></foot-guide>
  </div>
</template>

<script>
  import {mapMutations} from 'vuex'
  import headTop from 'src/components/header/head'
  import footGuide from 'src/components/footer/footGuide'
  import areaList from 'src/components/common/arealist'
  import {msiteAddress, cityGuess} from 'src/service/getData'
  import 'src/plugins/swiper.min.js'
  import 'src/style/swiper.min.css'

  export default {
    data() {
      return {
        geohash: '', // city页面传递过来的地址geohash
        msiteTitle: '请选择地址...', // msite页面头部标题
        hasGetData: false, //是否已经获取地理位置数据，成功之后再获取商铺列表信息
        imgBaseUrl: 'https://fuss10.elemecdn.com', //图片域名地址
      }
    },
    async beforeMount() {
      if (!this.$route.query.geohash) {
        const address = await cityGuess();
        this.geohash = address.latitude + ',' + address.longitude;
      } else {
        this.geohash = this.$route.query.geohash
      }
      //保存geohash 到vuex
      this.SAVE_GEOHASH(this.geohash);
      //获取位置信息
      let res = await msiteAddress(this.geohash);
      this.msiteTitle = res.name;
      // 记录当前经度纬度
      this.RECORD_ADDRESS(res);

      this.hasGetData = true;
    },
    mounted() {
    },
    components: {
      headTop,
      areaList,
      footGuide,
    },
    computed: {},
    methods: {
      ...mapMutations([
        'RECORD_ADDRESS', 'SAVE_GEOHASH'
      ]),
      // 解码url地址，求去restaurant_category_id值
      getCategoryId(url) {
        let urlData = decodeURIComponent(url.split('=')[1].replace('&target_name', ''));
        if (/restaurant_category_id/gi.test(urlData)) {
          return JSON.parse(urlData).restaurant_category_id.id
        } else {
          return ''
        }
      }
    },
    watch: {}
  }

</script>

<style lang="scss" scoped>
  @import 'src/style/mixin';

  .link_search {
    left: .8rem;
    @include wh(.9rem, .9rem);
    @include ct;
  }

  .msite_title {
    @include center;
    width: 50%;
    color: #fff;
    text-align: center;
    margin-left: -0.5rem;
    .title_text {
      @include sc(0.8rem, #fff);
      text-align: center;
      display: block;
    }
  }

  .msite_nav {
    padding-top: 2.1rem;
    background-color: #fff;
    border-bottom: 0.025rem solid $bc;
    height: 5.8rem;
    .area_type_container {
      height: 100%;
      .area_type_item {
        width: 20%;
        float: left;
        padding: 0.4rem 0rem;
      }
      .area_type_text {
        text-align: center;
        color: #666;
        font-size: 0.55rem;
      }
      .area_type_img {
        width: 100%;
        height: 1.8rem;
        text-align: center;
        -webkit-border-radius: 50%;
        -moz-border-radius: 50%;
        border-radius: 50%;
        img {
          width: 40%;
          height: 70%;
        }
      }
    }
    .fl_back {
      @include wh(100%, 100%);
    }
  }

  .food_types_container {
    display: flex;
    flex-wrap: wrap;
    .link_to_food {
      width: 25%;
      padding: 0.3rem 0rem;
      @include fj(center);
      figure {
        img {
          margin-bottom: 0.3rem;
          @include wh(1.8rem, 1.8rem);
        }
        figcaption {
          text-align: center;
          @include sc(0.55rem, #666);
        }
      }
    }
  }

  .area_list_container {
    margin-top: .4rem;
    border-top: 0.025rem solid $bc;
    background-color: #fff;
    .area_header {
      /*padding-top: 1.6rem;*/
      .area_icon {
        fill: #999;
        margin-left: 0.6rem;
        vertical-align: middle;
        @include wh(0.6rem, 0.6rem);
      }
      .area_header_title {
        color: #999;
        @include font(0.55rem, 1.6rem);
      }
    }
  }

</style>
