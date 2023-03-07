<template>
  <view>
    <!-- 使用自定义的搜索组件 -->
    <view class="search-box">
      <my-search @click="gotoSearch"></my-search>
    </view>
    <!-- 轮播图 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <swiper-item v-for="item in swiperList" :key="item.goods_id">
        <navigator class="swiper-item" :url="`/subpkg/goods_detail/goods_detail?goods_id=${item.goods_id}`">
          <image :src="item.image_src" mode=""></image>
        </navigator>
      </swiper-item>
    </swiper>

    <!-- 分类导航 -->
    <view class="nav-list">
      <view class="nav-item" v-for="(item, i) in navList" :key="i" @click="navClickHandler(item)">
        <image :src="item.image_src" class="nav-img"></image>
      </view>
    </view>

    <!-- 楼层区域 -->
    <view class="floor-list">
      <!-- 楼层 item 项 -->
      <view class="floor-item" v-for="(item, i) in floorList" :key="i">
        <!-- 楼层标题 -->
        <image :src="item.floor_title.image_src" class="floor-title"></image>
        <!-- 楼层图片区 -->
        <view class="floor-img-box">
          <!-- 左侧图片 -->
          <navigator class="left-img-box" :url="item.product_list[0].url">
            <image :src="item.product_list[0].image_src" :style="{width: item.product_list[0].image_width + 'rpx'}"
              mode="widthFix"></image>
          </navigator>
          <!-- 右侧小图片 -->
          <view class="right-img-box">
            <navigator class="right-img-item" v-for="(item2 , i2) in item.product_list" :key="i2" v-if="i2 !== 0"
              :url="item2.url">
              <image :src="item2.image_src" mode="widthFix" :style="{width: item2.image_width+'rpx'}"></image>
            </navigator>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
  // 导入自己封装的 mixin 模块
  import badgeMix from '@/mixins/tabbar-badge.js'

  export default {
    // 将 badgeMix 混入到当前的页面中进行使用
    mixins: [badgeMix],
    data() {
      return {
        swiperList: [], //轮播图数据
        navList: [], //首页分类导航数据
        floorList: [] // 楼层数据
      };
    },
    onLoad() {
      // 调用获取轮播图数据
      this.getSwiperList()
      // 调用首页分类导航接口获取数据
      this.getNavList()
      // 调用获取楼层数据方法
      this.getFloorList()
    },
    methods: {
      // 获取轮播图数据
      async getSwiperList() {
        const { data } = await uni.$http.get('/api/public/v1/home/swiperdata')
        // 请求失败处理
        if (data.meta.status !== 200) return uni.$showMsg()
        this.swiperList = data.message
      },

      // 获取分类导航数据
      async getNavList() {
        const { data } = await uni.$http.get('/api/public/v1/home/catitems')
        // 请求失败处理
        if (data.meta.status !== 200) return uni.$showMsg()
        this.navList = data.message
      },
      // 分类导航跳转
      navClickHandler(item) {
        if (item.name === '分类') {
          uni.switchTab({
            url: '/pages/cate/cate'
          })
        }
      },

      // 获取楼层数据
      async getFloorList() {
        const { data } = await uni.$http.get('/api/public/v1/home/floordata')
        // 请求失败处理
        if (data.meta.status !== 200) return uni.$showMsg()
        data.message.forEach(floor => {
          floor.product_list.forEach(prod => {
            prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
          })
        })

        this.floorList = data.message
      },

      // 跳转到分包中的搜索页面
      gotoSearch() {
        uni.navigateTo({
          url: '/subpkg/search/search'
        })
      }
    }
  }
</script>

<style lang="scss">
  // 轮播图
  swiper {
    height: 330rpx;

    .swiper-item,
    image {
      width: 100%;
      height: 100%;
    }
  }

  // 分类导航
  .nav-list {
    display: flex;
    justify-content: space-around;
    margin: 30rpx 0;

    & image {
      width: 128rpx;
      height: 140rpx;
    }
  }

  // 楼层样式
  .floor-list {
    .floor-title {
      width: 100%;
      height: 60rpx;
    }

    .floor-img-box {
      display: flex;
      padding-left: 10rpx;
    }

    .right-img-box {
      display: flex;
      flex-wrap: wrap;
      justify-content: space-around;
    }
  }

  .search-box {
    // 设置定位效果为“吸顶”
    position: sticky;
    // 吸顶的“位置”
    top: 0;
    // 提高层级，防止被轮播图覆盖
    z-index: 999;
  }
</style>
