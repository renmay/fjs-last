<template>
  <div>
    <head-top head-title="资讯头条" class="top_bar"></head-top>
    <!--分类导航-->
    <div id="indexHeader">
      <nav>
        <div class="nav_ul">
          <a href='javascript:;' :class='{active: currentIndex === -1}' @click.stop="first()">推荐</a>
          <a href='javascript:;' v-for="(item,index) in newsCatListData" :class='{active: currentIndex === item.id}' @click.stop="navClick(item.id)" :key="index">{{item.title}}</a>
        </div>
      </nav>
    </div>
    <div class="news-list">
      <div  v-for="item in newsListData" @click="showDetail(item)">
        <news-item  :data="item"></news-item>
      </div>
    </div>
    <foot-guide></foot-guide>
  </div>
</template>


<script>
  import {mapState, mapMutations} from 'vuex'
  import {getNewsList, getNewsCatList} from "../../service/getData";
  import headTop from 'src/components/header/head'
  import footGuide from 'src/components/footer/footGuide'
  import 'src/plugins/swiper.min.js'
  import 'src/style/swiper.min.css'
  import {showBack, animate} from 'src/config/mUtils'
  import {getStore, setStore} from "../../config/mUtils";
  import newsItem from 'src/components/news/news-item'

  export default {

    props: ['itemJson'],
    data(){
      return {
        currentIndex: -1,
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
      footGuide,
      newsItem,
    },
    computed: {
    },
    watch: {
      navClick(id){
        this.slideTo(id)
      }
    },
    methods: {
      ...mapMutations([
        'SAVE_NEWS'
      ]),
      url(item) {
        return `/news/newsDetail?id=${item.id}`
      },
      async initData() {
        //获取数据
        let data = await getNewsCatList();
        this.newsCatListData = data.list;
        let newData = await getNewsList();
        this.newsListData = newData.list;
        //开始监听scrollTop的值，达到一定程度后显示返回顶部按钮
        showBack(status => {
          this.showBackStatus = status;
        });
      },
      async first(){
        this.currentIndex = -1
        this.initData()
      },
      async navClick(id){
        // 通过传来的item的id去获取新的newList数据
        let data = await getNewsList(id);
        this.newsListData = data.list;
        //改变当前目录下标，匹配样式
        this.currentIndex = id
      },
      slideTo(index) {
        this.$nextTick(() => {
          let _container = $('.nav_ul')           // 获取滚动容器元素
          let _column = $('.nav_ul a').eq(index)  // 获取当前active栏目元素
          if (_column) {
            let move    // 最终滚动值
            let _container_width = _container.width()               // 容器宽度
            let _container_left = _container.scrollLeft()           // 容器当前滚动条的值
            let _column_width = _column.width()                     // 栏目宽度
            let _column_left = _column.position().left              // 栏目距离屏幕左边的距离
            let midWidth = (_container_width - _column_width) / 2   // 屏幕中心线的宽度
            // 容器滚动为0，并且active栏目位于中间线左边？滚动值为0 ：当前容器滚动值 + （active栏目相对于中间线的值，有正负）
            if (_container_left === 0 && _column_left <= midWidth) {
              move = 0
            } else {
              move = _container_left + (_column_left - midWidth)
            }
            _container.animate({ 'scrollLeft': move }, 300)
            setStore('navScrollLeft', move)       // 存计算后的值
          }
        })
      },
      // 滚动恢复
      scrollRecover() {
        let move = getStore('navScrollLeft')          // 取计算后的值
        if (move) {
          this.$nextTick(() => {
            $('.nav_ul').scrollLeft(move)
          })
        }
      },
      goTop() {
        $(`#index .${this.indexActive}`).animate({scrollTop: 0})
      },
      //显示详情页
      showDetail(item){
        this.SAVE_NEWS(item);
        this.preventRepeat = false;
        this.$router.push('/news/newsDetail');
      },
    },
    // activated钩子是要在keep-alive下才能使用
    activated() {
      this.scrollRecover()
    }
  }

</script>
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
    nav {
      margin-top: 2rem;
      position: relative;
      background-color: white;
      height: 44px;
      line-height: 44px;
      border-bottom: 1px solid #ddd;
      /*导航*/
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
          font-weight: 300;
          font-size: 0.65rem;
          color: #505050;
          white-space: nowrap;
          -webkit-tap-highlight-color: rgba(0, 0, 0, .3);
          text-decoration: none;
          &:last-child {
            margin-right: 5px;
          }
          &.active {
            color: #3190e8;
            border-bottom: 2px solid #3190e8;
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
  .news-list{
    padding-top:3.75rem;
  }
  img {
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
  .small-pic-container {
    margin:.3rem 0;
    height: 5.75rem;
    border:1px solid #3190e8;
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
      height: 100%;
      overflow: hidden;
      img {
        width: 100%;
        min-height:100%;
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
  .three-pics-container {
    margin:.3rem 0;
    height: 5.75rem;
    background-color:#3190e8;
    /*border:1px solid #3190e8;*/
    .list_img {
      width: 100%;
      height:60%;
      margin-top:2rem;
      ul {
        width: 100%;
        /*display: flex;*/
        font-size: 0;
      }
      li {
        display: inline-block;
        width: 33.3%;
        height: 100%;
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
  }
  .video-container {
      width: 100%;
      border-bottom:1px solid rebeccapurple;
    height:6rem;
    border-bottom:1px solid blue;
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
        border: 16px solid white;
        border-color: transparent transparent transparent rgba(255, 255, 255, .6);
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
