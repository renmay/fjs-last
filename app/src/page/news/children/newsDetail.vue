<template>
  <div class="news_detail_page">
    <head-top head-title="资讯详情" go-back='true'>
    </head-top>
    <div class="news_container">
      <div class="news_title">
        <p>{{newsDetail.title}}</p>
      </div>
      <div class="news_author">
        <div class="news_author_logo">
          <img src="../../../images/fenxiang.png" class="">
        </div>

        <div class="news_author_info">
          <div class="news_author_name">
            <span>{{newsDetail.source}}</span>
          </div>
          <div class="news_author_intro">
            3小时前 城市三叶草
          </div>
        </div>

        <div class="news_fellow">
          关注
        </div>
      </div>
      <div class="news_content">
        电子狗,驾驶安全预警,便携式导航仪,PND,DSA-善领科技
        善领科技是中国测速预警电子狗第一品牌,产品唯一通过公安部安全认证,全球首创DSA(驾驶安全预警系统),是国内导航地图电子眼数据唯一提供商,用户超过1200万,开发多项...
        www.zenlane.com/  - 百度快照 - 36条评价
        什么是DSA?_百度知道
        1个回答 - 回答时间: 2017年11月20日 - 129人觉得有用

        最佳答案: 主要适用于全身血管性疾病及肿瘤的检查及治疗。应用DSA进行介入治疗为心血管疾病的诊断和治疗开辟了一个新的领域。 随着神经介入放射技术飞速发展以及新型...
        更多关于dsa的问题>>
      </div>
    </div>


    <transition name="loading">
      <loading v-if="showLoading"></loading>
    </transition>
  </div>
</template>

<script>
  import {mapState, mapMutations} from 'vuex'
  import headTop from 'src/components/header/head'
  import {getImgPath} from 'src/components/common/mixin'
  import {getOrderDetail} from 'src/service/getData'
  import loading from 'src/components/common/loading'
  import BScroll from 'better-scroll'
  import {imgBaseUrl} from 'src/config/env'


  export default {

    data() {
      return {
        showLoading: true, //显示加载动画
        orderData: null,
        imgBaseUrl
      }
    },
    mounted() {
      this.initData();
    },
    mixins: [getImgPath],
    components: {
      headTop,
      loading,
    },
    computed: {
      ...mapState([
        'newsDetail', 'geohash', 'userInfo'
      ]),
    },
    methods: {
      async initData() {
        console.log(this.newsDetail)
      },
    },
    watch: {
      userInfo: function (value) {
        if (value && value.userId) {
          this.initData();
        }
      }
    }
  }
</script>

<style lang="scss" scoped>
  @import 'src/style/mixin';

  .news_detail_page {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    padding-top: 1.95rem;
    background-color: white;
    .news_container {
      padding: .3rem;
      .news_title {
        padding-bottom: .5rem;
        font-weight: bold;
      }
      .news_author {
        .news_author_logo {
          width: 15%;
          float: left;
          height: 2rem;
          margin-right: .5rem;
          img {
            width: 100%;
            height: 100%;
          }
        }
        .news_author_info {
          float: left;
          width: 60%;
          height: 2rem;
          .news_author_name {
            line-height: 1rem;
            font-size: .8rem;
          }
          .news_author_intro {
            line-height: 1rem;
            color: gray;
            font-size: .6rem;
          }
        }
        .news_fellow {
          width: 20%;
          float: left;
          display: inline-block;
          background-color: #f8414b;
          border-radius: .3em;
          padding: .2rem 0;
          text-align: center;
          color: white;
          font-size: .8rem;
        }
        .news_fellow:active {
          border-color: rgba(0, 0, 0, .3);
          background: #bbb;
        }
      }
      .news_content{
        padding-top: 2.8rem;
        font-size: .7rem;
        text-indent: .6rem;
      }
    }
  }


</style>
