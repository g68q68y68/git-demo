<template>
	<view 
	v-if="visible" 
	class="fullscreen-view" 
	@click="close" 
	@touchstart="touchStart" 
	@touchmove="touchMove" 
	@touchend="touchEnd"
	>
		<view v-if="visible" class="preview-modal" @click="closePreview">
			<view class="preview-content">
				<image 
				class="popup-image"
				:style="{ transform: `scale(${scale})` }"
				v-if="item.type === 'image'" 
				:src="item.path" 
				mode="widthFix" />
				
				<video 
				class="popup-video"
				:style="{ transform: `scale(${scale})` }"
				v-else-if="item.type === 'video'" 
				:src="item.path" 
				controls></video>
			</view>
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
		  item: {
		    type: Object,
		    required: true
		  },
		},
		data() {
			return {
				startY: 0, // 记录触摸起始点
				currentY: 0, // 当前的触摸点
				scale: 1, // 图片缩放比例
				showPopup: true, // 控制弹窗显示
			};
		},

		methods: {
			closePreview() {
			  this.visible = false
			},
			touchStart(e) {
				this.startY = e.touches[0].pageY; // 记录手指初始位置
			},
			touchMove(e) {
				this.currentY = e.touches[0].pageY;
				const distance = this.currentY - this.startY; // 计算移动距离
				if (distance > 0) { // 下拉手势
					this.scale = Math.max(1 - distance / 500, 0.5); // 根据移动距离缩小图片，最小缩放至 0.5
				}
			},
			touchEnd() {
				if (this.scale < 0.8) {
					this.closePopup(); // 缩小到一定比例时关闭弹窗
				} else {
					this.scale = 1; // 没有达到关闭条件则恢复图片大小
				}
			},
			closePopup() {
				this.showPopup = false; // 关闭弹窗
			}
		}
	}
</script>

<style lang="scss">
	.preview-modal {
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background-color: rgba(0, 0, 0, 0.7);
		display: flex;
		justify-content: center;
		align-item: center;
		z-index: 1000;
	}

	.preview-content {
		max-width: 90%;
		max-height: 90%;
		display: flex;
		justify-content: center;
		align-item: center;
		background-color: #fff;
		border-radius: 12rpx;
		overflow: hidden;
		padding: 10rpx;
	}

	.preview-content image {
		max-width: 100%;
		max-height: 80vh;
		width: auto;
		height: auto;
		object-fit: contain;
	}

	.preview-content video {
		max-width: 100%;
		max-height: 80vh;
		width: auto;
		height: auto;
	}
	.popup-image,
	.popup-video{
	  width: 80%;
	  transition: transform 0.3s ease; // 平滑的缩放动画效果
	}
</style>