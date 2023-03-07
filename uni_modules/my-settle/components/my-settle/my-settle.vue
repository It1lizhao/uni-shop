<template>
  <!-- 最外层的容器 -->
  <view class="my-settle-container">
    <!-- 全选区域 -->
    <label class="radio" @click="changeAllState">
      <radio color="#C00000" :checked="isFullCheck" /><text>全选</text>
    </label>

    <!-- 合计区域 -->
    <view class="amount-box">
      合计:<text class="amount">￥{{checkedGoodsAmount}}</text>
    </view>

    <!-- 结算按钮 -->
    <view class="btn-settle">结算({{checkedCount}})</view>
  </view>
</template>

<script>
  import { mapGetters } from 'vuex'
  export default {
    data() {
      return {}
    },
    computed: {
      // 1. 将 total 映射到当前组件中
      ...mapGetters('m_cart', ['checkedCount', 'total', 'checkedGoodsAmount']),
      // 2. 是否全选
      isFullCheck() {
        return this.total === this.checkedCount
      }
    },
    methods: {
      // 实现商品的全选/反选功能
      changeAllState() {
        // 修改购物车中所有商品的选中状态
        // !this.isFullCheck 表示：当前全选按钮的状态取反之后，就是最新的勾选状态
        this.$store.commit('m_cart/updateAllGoodsState', !this.isFullCheck)
      }
    }
  }
</script>

<style lang="scss">
  .my-settle-container {
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 100rpx;
    // 将背景色从 cyan 改为 white
    background-color: white;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding-left: 10rpx;
    font-size: 28rpx;

    .radio {
      display: flex;
      align-items: center;
    }

    .amount {
      color: #c00000;
    }

    .btn-settle {
      height: 100rpx;
      min-width: 200rpx;
      background-color: #c00000;
      color: white;
      line-height: 100rpx;
      text-align: center;
      padding: 0 20rpx;
    }
  }
</style>
