<template>
  <view 
  v-if="visible" 
  class="fullscreen-view" 
  @click="close"
  
  >
    <view class="fullscreen-content">
      <swiper
        :current="currentIndex"
        @change="handleSwiperChange"
        class="fullscreen-swiper"
      >
        <swiper-item 
		v-for="(item, index) in items" 
		:key="index"
		@touchstart="touchStart"
		@touchmove="touchMove" 
		@touchend="touchEnd"
		>
          <image 
		  :style="{ transform: `scale(${scale})` }"
		  :src="item.path" 
		  mode="aspectFit" />
		<!--  <image 
		  :style="{ transform: `scale(${scale})` }"
		  v-if="item.type === 'image'" 
		  :src="item.path" 
		  mode="aspectFit" /> -->
    <!--      <video 
		  :style="{ transform: `scale(${scale})` }"
		  v-else-if="item.type === 'video'" 
		  :src="item.path"
		  :autoplay="true"
		  :muted="true"
			></video> -->
        </swiper-item>
      </swiper>
    </view>
  </view>
</template>

<script>
export default {
  props: {
    visible: {
      type: Boolean,
      required: true
    },
    items: {
      type: Array,
      required: true
    },
    initialIndex: {
      type: Number,
      require:true,
	  default: 0
    },
  },
  data() {
    return {
      currentIndex: 0,
	  startY: 0, // 记录触摸起始点
	  currentY: 0, // 当前的触摸点
	  scale: this.scales, // 图片缩放比例
    }
  },
  watch:{
	  
	initialIndex:{
		handler(value){
			this.currentIndex = value
	
		},
		immediate:true
		
	}
  },
  methods: {
    close() {
		this.scale = 1
      // this.visible = false
	  this.$emit('close')
    },
    handleSwiperChange(e) {
      this.currentIndex = e.detail.current
    },
	touchStart(e) {
		this.startY = e.touches[0].pageY; // 记录手指初始位置
	},
	touchMove(e) {
		this.currentY = e.touches[0].pageY;
		const distance = this.currentY - this.startY; // 计算移动距离
		if (distance > 0) { // 下拉手势
			this.scale = Math.max(1 - distance / 500, 0.7); // 根据移动距离缩小图片，最小缩放至 0.5
		}
	},
	touchEnd() {
		if (this.scale < 0.8) {
			this.close(); // 缩小到一定比例时关闭弹窗
		} else {
			this.scale = 1; // 没有达到关闭条件则恢复图片大小
		}
	},
  }
}
</script>

<style lang="scss">
.fullscreen-view {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  // background-color: rgba(0, 0, 0, 0.8);
  background-color: rgba(0, 0, 0, 0.5); /* 半透明灰色背景 */
    backdrop-filter: blur(10px); /* 添加模糊效果 */
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.fullscreen-content {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.fullscreen-swiper {
  width: 100%;
  height: 100%;
}

.fullscreen-swiper image,
.fullscreen-swiper video {
  width: 100%;
  height: 100%;
  object-fit: contain;
}
</style>