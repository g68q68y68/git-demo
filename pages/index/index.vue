<template>
  <view class="content">
    <view class="stars"></view>
    <view class="twinkling"></view>
    <view v-for="(bubble, index) in bubbles" :key="index" 
          class="bubble" 
          :class="`bubble-${index + 1}`" 
          :style="bubble.style"
		  @click="optionFunction(bubble.text)"
		  >
      {{ bubble.text }}
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      bubbles: [
        { text: '相册', style: { left: '0rpx', top: '0rpx' } },
        { text: '添加', style: { left: '250rpx', top: '50rpx' } },
        { text: '浏览', style: { left: '340rpx', top: '540rpx' } },
        { text: '视频', style: { left: '200rpx', top: '300rpx' } }
      ]
    }
  },
  onLoad() {
    this.startBubbleMovement()
  },
  methods: {
	optionFunction(value){
		if (value === '相册') {
		    uni.navigateTo({
		      url: "/pages/viewPhotoAndVideo/viewPhotoAndVideo"
		    })
		  } else if (value === '添加') {
		    uni.navigateTo({
		      url: "/pages/addMedia/addMedia"
		    })
		  }
		
	},
	  
    startBubbleMovement() {
      setInterval(() => {
        this.bubbles.forEach((bubble, index) => {
          this.moveBubble(index)
        })
      }, 2000)
    },
    moveBubble(index) {
      const maxX = uni.getSystemInfoSync().windowWidth - 150
      const maxY = uni.getSystemInfoSync().windowHeight - 150
      const newX = Math.random() * maxX
      const newY = Math.random() * maxY
      this.bubbles[index].style = {
        left: `${newX}px`,
        top: `${newY}px`,
        transition: 'all 2s ease-in-out'
      }
    }
  }
}
</script>

<style lang="scss">
.content {
  position: relative;
  height: 100vh;
  overflow: hidden;
  background: #000033;
}

@function multiple-box-shadow($n) {
  $value: '#{random(2000)}px #{random(2000)}px #FFF';
  @for $i from 2 through $n {
    $value: '#{$value} , #{random(2000)}px #{random(2000)}px #FFF';
  }
  @return unquote($value);
}

$shadows-small: multiple-box-shadow(1000);
$shadows-medium: multiple-box-shadow(400);

.stars {
  width: 1px;
  height: 1px;
  background: transparent;
  box-shadow: $shadows-small;
  animation: animateStars 50s linear infinite;

  &:after {
    content: " ";
    position: absolute;
    top: 2000px;
    width: 1px;
    height: 1px;
    background: transparent;
    box-shadow: $shadows-small;
  }
}

.twinkling {
  width: 2px;
  height: 2px;
  background: transparent;
  box-shadow: $shadows-medium;
  animation: animateStars 100s linear infinite;

  &:after {
    content: " ";
    position: absolute;
    top: 2000px;
    width: 2px;
    height: 2px;
    background: transparent;
    box-shadow: $shadows-medium;
  }
}

@keyframes animateStars {
  from {
    transform: translateY(0px);
  }
  to {
    transform: translateY(-2000px);
  }
}

.bubble {
  position: absolute;
  width: 300rpx;
  height: 300rpx;
  border-radius: 50%;
  text-align: center;
  line-height: 300rpx;
  font-size: 36rpx;
  font-weight: bold;
  color: #fff;
  text-shadow: 0 1px 2px rgba(0,0,0,0.1);
  transition: all 0.3s ease-in-out;
  background: radial-gradient(circle at 30% 30%, 
    rgba(255, 255, 255, 0.8) 0%, 
    rgba(255, 255, 255, 0.3) 40%, 
    rgba(255, 255, 255, 0.1) 60%, 
    rgba(255, 255, 255, 0) 70%);
  box-shadow: 
    inset 0 0 20px rgba(255, 255, 255, 0.5),
    inset 0 0 40px rgba(255, 255, 255, 0.3),
    inset 0 0 60px rgba(255, 255, 255, 0.1),
    0 0 20px rgba(255, 255, 255, 0.5),
    0 0 40px rgba(255, 255, 255, 0.3),
    0 0 60px rgba(255, 255, 255, 0.1);

  &:hover {
    transform: scale(1.05);
  }
}

.bubble-1 {
  background: radial-gradient(circle at 30% 30%, 
    rgba(0, 255, 255, 0.8) 0%, 
    rgba(0, 255, 255, 0.3) 40%, 
    rgba(0, 255, 255, 0.1) 60%, 
    rgba(0, 255, 255, 0) 70%);
  box-shadow: 
    inset 0 0 20px rgba(0, 255, 255, 0.5),
    inset 0 0 40px rgba(0, 255, 255, 0.3),
    inset 0 0 60px rgba(0, 255, 255, 0.1),
    0 0 20px rgba(0, 255, 255, 0.5),
    0 0 40px rgba(0, 255, 255, 0.3),
    0 0 60px rgba(0, 255, 255, 0.1);
  animation: pulse-cyan 4s infinite alternate;
}

.bubble-2 {
  background: radial-gradient(circle at 30% 30%, 
    rgba(255, 0, 255, 0.8) 0%, 
    rgba(255, 0, 255, 0.3) 40%, 
    rgba(255, 0, 255, 0.1) 60%, 
    rgba(255, 0, 255, 0) 70%);
  box-shadow: 
    inset 0 0 20px rgba(255, 0, 255, 0.5),
    inset 0 0 40px rgba(255, 0, 255, 0.3),
    inset 0 0 60px rgba(255, 0, 255, 0.1),
    0 0 20px rgba(255, 0, 255, 0.5),
    0 0 40px rgba(255, 0, 255, 0.3),
    0 0 60px rgba(255, 0, 255, 0.1);
  animation: pulse-magenta 4s infinite alternate;
}

.bubble-3 {
  background: radial-gradient(circle at 30% 30%, 
    rgba(255, 255, 0, 0.8) 0%, 
    rgba(255, 255, 0, 0.3) 40%, 
    rgba(255, 255, 0, 0.1) 60%, 
    rgba(255, 255, 0, 0) 70%);
  box-shadow: 
    inset 0 0 20px rgba(255, 255, 0, 0.5),
    inset 0 0 40px rgba(255, 255, 0, 0.3),
    inset 0 0 60px rgba(255, 255, 0, 0.1),
    0 0 20px rgba(255, 255, 0, 0.5),
    0 0 40px rgba(255, 255, 0, 0.3),
    0 0 60px rgba(255, 255, 0, 0.1);
  animation: pulse-yellow 4s infinite alternate;
}

.bubble-4 {
  background: radial-gradient(circle at 30% 30%, 
    rgba(0, 255, 0, 0.8) 0%, 
    rgba(0, 255, 0, 0.3) 40%, 
    rgba(0, 255, 0, 0.1) 60%, 
    rgba(0, 255, 0, 0) 70%);
  box-shadow: 
    inset 0 0 20px rgba(0, 255, 0, 0.5),
    inset 0 0 40px rgba(0, 255, 0, 0.3),
    inset 0 0 60px rgba(0, 255, 0, 0.1),
    0 0 20px rgba(0, 255, 0, 0.5),
    0 0 40px rgba(0, 255, 0, 0.3),
    0 0 60px rgba(0, 255, 0, 0.1);
  animation: pulse-green 4s infinite alternate;
}

@keyframes pulse-cyan {
  0% {
    filter: hue-rotate(0deg) brightness(1);
  }
  100% {
    filter: hue-rotate(180deg) brightness(1.2);
  }
}

@keyframes pulse-magenta {
  0% {
    filter: hue-rotate(180deg) brightness(1);
  }
  100% {
    filter: hue-rotate(360deg) brightness(1.2);
  }
}

@keyframes pulse-yellow {
  0% {
    filter: hue-rotate(90deg) brightness(1);
  }
  100% {
    filter: hue-rotate(270deg) brightness(1.2);
  }
}

@keyframes pulse-green {
  0% {
    filter: hue-rotate(270deg) brightness(1);
  }
  100% {
    filter: hue-rotate(450deg) brightness(1.2);
  }
}
</style>