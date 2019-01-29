<template>
  <div>
    <head-top head-title="资讯头条" go-back='true' class="top_bar"></head-top>
    <!--分类导航-->
    <div id="indexHeader">
        <div class="nav_ul">
          <a href='javascript:;' v-for="(item,index) in newsCatListData" :class='{active: indexActive == item.title}' @click.stop="navClick(item.classpath)" :key="index">{{item.title}}</a>
        </div>
    </div>

    <!--内容-->
    <ul class="newsList">
      <template v-for="section in newsListData">
        <!-- 视频 -->
        <li v-if="section.videoUrl">
          <router-link :to="url(section)" class='video'>
            <div class="video_wrapper">
              <div class="video_info">
                <div class="video_title">
                  <p v-html="section.title"></p>
                </div>
                <div class="totalTime">{{section.playtime}}</div>
                <img v-lazy.container="section.titlepic">
              </div>
              <div class="playRound">
                <div class="playSan"></div>
              </div>
            </div>
            <list-info :json='section'></list-info>
          </router-link>
        </li>
        <!-- 1张大图 -->
        <li v-else-if="section.ptitlepic">
          <router-link :to="url(section)" class='oneLarge'>
            <div class="news_title">
              <h3 v-html="section.title"></h3>
            </div>
            <div class='news_img'>
              <img v-lazy.container='section.ptitlepic'>
            </div>
            <list-info :json='section'></list-info>
          </router-link>
        </li>
        <!-- 3张小图 -->
        <li v-else-if="section.titlepic3">
          <router-link :to="url(section)" class='threeSmall'>
            <div class="news_title">
              <h3 v-html="section.title"></h3>
            </div>
            <div class='list_img'>
              <ul>
                <li><img v-lazy.container='section.titlepic'></li>
                <li><img v-lazy.container='section.titlepic2'></li>
                <li><img v-lazy.container='section.titlepic3'></li>
              </ul>
            </div>
            <list-info :json='section'></list-info>
          </router-link>
        </li>
        <!-- 1张小图 -->
        <li v-else-if="section.titlepic">
          <router-link :to="url(section)" class='oneSmall'>
            <div class="news_title">
              <h3 v-html="section.title"></h3>
              <list-info :json='section'></list-info>
            </div>
            <div class='news_img'>
              <img v-lazy.container='section.titlepic'>
            </div>
          </router-link>
        </li>
        <!-- 文字 -->
        <li v-else-if='section.title'>
          <router-link :to="url(section)" class='text'>
            <h3 v-html="section.title"></h3>
            <list-info :json='section'></list-info>
          </router-link>
        </li>
        <li v-else-if='section.type' id="lookHere">
          <p>上次看到这里，点击刷新 <i class="icon-refresh"></i></p>
        </li>
        <li v-else-if='section.type'>
          <p>上次看到这里，点击刷新 <i class="icon-refresh"></i></p>
        </li>
      </template>
    </ul>
    <foot-guide></foot-guide>
  </div>
</template>

<script>

  import {getNewsList, getNewsCatList} from "../../service/getData";
  import { mapState, mapGetters, mapMutations } from 'vuex'
  // import {imgBaseUrl} from 'src/config/env'
  import headTop from 'src/components/header/head'
  import footGuide from 'src/components/footer/footGuide'
  import shopList from 'src/components/common/shoplist'
  import {newsAddress, newsFoodTypes, cityGuess} from 'src/service/getData'
  import 'src/plugins/swiper.min.js'
  import 'src/style/swiper.min.css'
  // import * as logger from "less";
  import indexHeader from 'src/components/news/index_header'
  import swiperContainer from 'src/components/news/swiperContainer'
  import newList from 'src/components/news/newsList'
  import {showBack, animate} from 'src/config/mUtils'

  export default {
    props: ['itemJson'],
    data(){
      return {
        newsListData: [],
        newsCatListData: [],
        newsCatId: '',
        geohash: '', // city页面传递过来的地址geohash
        newsTitle: '请选择地址...', // news页面头部标题
        foodTypes: [], // 食品分类列表
        hasGetData: false, //是否已经获取地理位置数据，成功之后再获取商铺列表信息
        imgBaseUrl: 'https://fuss10.elemecdn.com', //图片域名地址
      }
    },
    mounted(){
      this.initData();
    },
    components: {
      headTop,
      shopList,
      footGuide,
      indexHeader,
      swiperContainer,
      newList

    },
    computed: {
      ...mapState('index', [
        'indexActive',
        'indexColumn'
      ]),
      ...mapGetters('index', [
        'activeMeta'
      ])
    },
    watch: {
      indexActive() {
        this.slideTo(this.activeMeta.index)
      }
    },
    methods: {
      url(item) {
        return `/detail?classid=${item.classid}&id=${item.id}&datafrom=${item.datafrom}`
      },
      async initData() {
        //获取数据
        let res = await getNewsCatList();
        this.newsCatListData = res.page.list;

        let newsRes = await getNewsList();
        this.newsListData = newsRes.page.list;
        console.log(this.newsCatListData)
        console.log(this.newsListData)
        if (res.length < 20) {
          this.touchend = true;
        }
        //开始监听scrollTop的值，达到一定程度后显示返回顶部按钮
        showBack(status => {
          this.showBackStatus = status;
        });
      },
    },
    watch: {

    }
  }

</script>

<style lang="scss" scoped>
  @import 'src/style/mixin';
  .link_search{
    left: .8rem;
    @include wh(.9rem, .9rem);
    @include ct;
  }
  .news_title{
    @include center;
    width: 50%;
    color: #fff;
    text-align: center;
    margin-left: -0.5rem;
    .title_text{
      @include sc(0.8rem, #fff);
      text-align: center;
      display: block;
    }
  }
  .news_nav{
    padding-top: 2.1rem;
    background-color: #fff;
    border-bottom: 0.025rem solid $bc;
    height: 4rem;
    .swiper-container{
      @include wh(100%, auto);
      padding-bottom: 0.6rem;
      .swiper-pagination{
        bottom: 0.2rem;
      }
    }
    .fl_back{
      @include wh(100%, 100%);
    }
  }
  .food_types_container{
    display:flex;
    flex-wrap: wrap;
    .link_to_food{
      width: 25%;
      padding: 0.3rem 0rem;
      @include fj(center);
      figure{
        img{
          margin-bottom: 0.3rem;
          @include wh(1.8rem, 1.8rem);
        }
        figcaption{
          text-align: center;
          @include sc(0.55rem, #666);
        }
      }
    }
  }
  .shop_list_container{
    margin-top: .4rem;
    border-top: 0.025rem solid $bc;
    background-color: #fff;
    .shop_header{
      .shop_icon{
        fill: #999;
        margin-left: 0.6rem;
        vertical-align: middle;
        @include wh(0.6rem, 0.6rem);
      }
      .shop_header_title{
        color: #999;
        @include font(0.55rem, 1.6rem);
      }
    }
  }
</style>
<style lang='stylus'>
  small_height=1.96875rem
  larger_height=4.6875rem
  /*分类*/
  #indexHeader {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    z-index: 999;
    overflow: hidden;
    header {
      display: block;
      position: relative;
      overflow: hidden;
      background-color: #00939c;
      color: #fff;
      &.fixed{
        position: fixed;
        top: 0;
        left: 0;
        right: 0;
        z-index: 1000;
      }
      .title {
        background-size: 145px;
      }
      .search_btn {
        background-size: 24px;
      }
      .top_bar {
        position: relative;
        height: 44px;
        line-height: 44px;
        user-select:none;
        a{
          display: block;
          width: 100%;
          height: 100%;
          color: inherit;
          font-size: inherit;
          font-weight: inherit;
        }
        .abs_l,.abs_m,.abs_r {
          position: absolute;
          top: 0;
          width: 44px;
          height: 100%;
          font-size: inherit;
          color: inherit;
          text-align: center
        }
        .abs_l {
          left: 0;
          z-index: 1000;
        }
        .abs_m {
          width: 100%;
          font-weight: 700;
          z-index: 999;
        }
        .abs_r {
          right: 0;
          z-index: 1000;
        }
      }
    }
    nav {
      margin-top: 44px;
      position: relative;
      height: 44px;
      line-height: 44px;
      background-color: #f4f5f6;
      border-bottom: 1px solid #ddd;
      /*更多栏目*/
      .nav_ul {
        overflow-x: auto;
        -webkit-overflow-scrolling: touch;
        white-space: nowrap;
        &::-webkit-scrollbar {
          width: 0px;
          height: 0px;
        }
        a {
          display: inline-block;
          padding-left: 10px;
          padding-right: 10px;
          margin-left: 5px;
          height: 36px;
          line-height: 36px;
          font-size: 17px;
          color: #505050;
          white-space: nowrap;
          -webkit-tap-highlight-color: rgba(0, 0, 0, .3);
          text-decoration: none;
          &:last-child {
            margin-right: 5px;
          }
          &.active {
            color: #00939c;
            border-bottom: 2px solid #00939c;
          }
        }
      }
      .nav_menu {
        position: absolute;
        top: 0;
        right: 0;
        height: 100%;
        .shadow {
          position: absolute;
          width: 10px;
          height: 100%;
          left: -10px;
          background-size: contain;
          background-color: rgba(244, 245, 246, .3);
        }
        .more_btn {
          display: block;
          width: 40px;
          height: 100%;
          background-size: 20px;
          background-color: #f4f5f6;
        }
      }
    }
  }



  /*资讯内容*/
  .newsList{
    padding-top:3.75rem;
    li {
      margin: 0 0.4rem;
      border-bottom: 1px solid hsla(0, 0%, 87%, .6);
    }
    a {
      display: block;
      width: 100%;
      padding: 0.427rem 0;
      outline: none;
      color: #131313;
      -webkit-tap-highlight-color: rgba(0, 0, 0, .1);
      text-decoration: none;
    }
    a:visited h3 {
      color: #999;
    }
    a highlight{
        color: #fe3333!important;
    font-weight: bold;
  }
  img {
    background: #ddd;
  }
  img[lazy=loading] {
    height: 100%;
  }
  h3 {
    white-space: normal;
    font-size: 17px;
    line-height: 21px;
    color: #222;
    font-weight: 400;
    display: -webkit-box;
    -webkit-line-clamp: 3;
    text-overflow: ellipsis;
    -webkit-box-orient: vertical;
    overflow: hidden;
  }
  .oneSmall {
    font-size: 0;
    & > div{
      display: inline-block;
      vertical-align: middle;
    }
    .news_title {
      width: 67%;
      overflow: hidden;
      h3 {
        margin-right: 0.32rem;
      }
    }
    .news_img {
      width: 33%;
      height: small_height;
      overflow: hidden;
      img {
        width: 100%;
        min-height: small_height;
      }
    }
  }
  .oneLarge {
    .news_img {
      width: 100%;
      margin-top: 0.16rem;
      overflow: hidden;
      height: larger_height;
    }
    img {
      width: 100%;
      min-height: larger_height;
    }
  }
  .threeSmall {
  }
  .list_img {
      width: 100%;
      margin-top: 0.16rem;
      ul {
        width: 100%;
        /*display: flex;*/
        font-size: 0;
      }
      li {
        display: inline-block;
        width: 33.3%;
        height: small_height;
        overflow: hidden;
        margin: 0!important;
        /*flex: 1;*/
      }
      li:nth-child(2) {
        padding: 0 2px;
      }
      img {
        width: 100%;
        min-height: small_height;
      }
  }
  .video {
    video {
      width: 100%;
    }
    .video_wrapper {
      width: 100%;
      height: larger_height;
      position: relative;
      overflow: hidden;
      color: #999;
      .video_info {
        width: 100%;
        height: 100%;
        position: absolute;
        left: 0;
        top: 0;
        img {
          position: absolute;
          width: 100%;
          min-height: larger_height;
          display: block;
          left: 0;
          top: 0;
          z-index: 111;
        }
      }
      .video_title {
        position: absolute;
        width: 100%;
        height: 80px;
        top: 0;
        left: 0;
        color: #fff;
        background-image: -webkit-gradient(linear, left top, left bottom, color-stop(0, rgba(0, 0, 0, .5)), color-stop(100%, transparent));
        z-index: 222;
        p {
          width: 100%;
          font-size: 14px;
          line-height: 24px;
          padding: 8px 0.4rem 0;
          margin: 0;
          color: #fff!important;
        }
      }
      .totalTime {
        position: absolute;
        display: inline-block;
        width: 40px;
        right: 5px;
        bottom: 5px;
        background: rgba(0, 0, 0, .5);
        color: #fff;
        font-size: 12px;
        text-align: center;
        height: 20px;
        line-height: 20px;
        border-radius: 10px;
        z-index: 222;
      }
      .playRound {
        position: absolute;
        width: 50px;
        height: 50px;
        left: 50%;
        top: 50%;
        margin-left: -25px;
        margin-top: -25px;
        border-radius: 50%;
        background: rgba(0, 0, 0, .3);
        z-index: 222;
        border: 1px solid #fff;
      }
      .playSan {
        position: absolute;
        width: 0;
        height: 0;
        font-size: 0;
        border: 16px solid transparent transparent transparent rgba(255, 255, 255, .6);
        left: 50%;
        top: 50%;
        margin-left: -5px;
        margin-top: -16px;
      }
    }
  }
  #lookHere {
    background: #f4f5f6;
    border: none !important;
    margin: 0 !important;
    p {
      font-size: 12px;
      line-height: 18px;
      color: #00939c;
      text-align: center;
      user-select: none;
      margin: 0;
      padding: 10px 0;
    }
  }
</style>
