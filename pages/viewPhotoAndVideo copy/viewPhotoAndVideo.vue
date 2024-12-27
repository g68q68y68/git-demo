<template>
	<view class="container">
		<!--   <view class="header">
      <text class="title">照片和视频</text>
    </view> -->
		<view class="top">
			<view class="back" @click="backMain">
				<text class="iconfont icon-shouye"></text>
			</view>
			<view class="select" @click="selectCount > 0 && selectFile()">选择</view>
		</view>

		<u-button text="月落"></u-button>
		<view class="content">
			<view class="gallery-grid" :style="{ '--columns': columns }">
				<view v-for="(item, index) in mediaItems" :key="index" class="gallery-item"
					@touchstart="handleTouchStart" @touchmove="handleTouchMove" @touchend="handleTouchEnd">

			<!-- 		<MaskItem 
					:firstCount="mediaItems.length" 
					:secondCount="selectCount" 
					:allSelect="allSelect"
					:openMask="openMask" 
					:keyValue="index" 
					@isSelect="isSelect"
					
					:isSelect="item.isSelect"
					></MaskItem> -->
					
					<view v-if="item.isSelect" class="mask" @click="ischange">
							<view class="mask-circle" >
								<text  class="iconfont icon-xuanzhong" style="color: deepskyblue;"></text>
							</view>
						</view>

					<image @click="showFullscreen(index)" :src="item.path" mode="aspectFill" />
					<!-- 		<image @click="showFullscreen(index)" v-if="item.type === 'image'" :src="item.path" mode="aspectFill" />
					<video @click="showFullscreen(index)" v-else-if="item.type === 'video'" :src="item.path"></video> -->
				</view>
			</view>
		</view>
		<FullscreenView :visible="isFullscreenVisible" :items="mediaItems" :initialIndex="currentFullscreenIndex"
			@close="closeFullscreen" />


		<!-- 下方标题 -->
		<view class="buttom" v-if="openMask === true">
			<view class="allselect " @click="allselect">
				<text v-if="!allSelect" class="iconfont icon-quanxuan"></text>
				<text v-else class="iconfont icon-quanxuan" style="color: deepskyblue;"></text>
			</view>
			<view v-if="selectCount === 0">选择项目</view>
			<view v-else-if="selectCount !== 0">已选择 {{selectCount}}项</view>
			<view class="delete" @click="showDeleteModal">
				<text v-if="selectCount === 0" class="iconfont icon-shanchu1"></text>
				<text v-else class="iconfont icon-shanchu1" style="color: deepskyblue;"></text>
			</view>
		</view>


		<!-- 删除确认模态框 -->
		<uni-popup ref="deleteModal" type="dialog">
			<uni-popup-dialog type="warning" cancelText="取消" confirmText="确认" title="删除确认" content="确定要删除选中的项目吗？"
				@confirm="confirmDelete" @close="closeDeleteModal"></uni-popup-dialog>
		</uni-popup>
	</view>
</template>

<script>
	export default {

		data() {
			return {
				mediaItems: [],
				columns: 3,
				isFullscreenVisible: false,
				currentFullscreenIndex: 0,
				touchStartTime: 0,
				touchStartDistance: 0,
				isZooming: false,
				allSelect: false,
				selectCount: 0,
				openMask: false,
				selectIndexList: [],

			}
		},
		onLoad() {
			this.loadMediaItems()
		},
		watch: {
			selectCount(value) {
				if (value === this.mediaItems.length) {
					this.allSelect = true

				} else {
					this.allSelect = false
				}
			}
		},
		methods: {
			isSelect(index) {
				let obj = this.selectIndexList.includes(index)
				if (obj) {
					const num = this.selectIndexList.indexOf(index)
					this.selectIndexList.splice(num, 1)
					this.selectCount--
				} else {
					this.selectIndexList.unshift(index)
					this.selectCount++

				}
			},
			backMain() {
				uni.navigateTo({
					url: '/pages/index/index'
				})
			},
			selectFile() {
				if (this.openMask === true) {
					this.openMask = false
				} else {
					this.openMask = true
				}
				this.selectCount = 0
				this.selectIndexList = []
			},
			allselect() {
				if (this.allSelect === false) {
					let count = this.mediaItems.length
					this.selectCount = count
					this.selectIndexList = []
					for (let i = 0; i < count; i++) {
						this.selectIndexList.shift(i)
					}
					this.allSelect = true

				} else {
					this.selectIndexList = []
					this.selectCount = 0
					this.allSelect = false

				}

			},
			loadMediaItems() {
				// const savedPaths = uni.getStorageSync('savedMediaPaths') || [];
				// this.mediaItems = savedPaths;

				const savedPaths = uni.getStorageSync('savedMediaPaths') || []
				this.mediaItems = savedPaths.map(path => ({
					path,
					selected: false
				}))
			},

			showDeleteModal() {
				if (this.selectCount > 0) {
					this.$refs.deleteModal.open()
				}
			},
			closeDeleteModal() {
				this.$refs.deleteModal.close()
			},
			confirmDelete() {
				const itemsToDelete = this.mediaItems.filter(item => item.selected)
				itemsToDelete.forEach(item => this.deleteMediaItem(item.path))
				this.closeDeleteModal()
			},
			deleteMediaItem(path) {
				// 删除文件
				uni.removeSavedFile({
					filePath: path,
					success: () => {
						console.log('文件删除成功:', path)
						// 从本地存储中移除路径
						let savedPaths = uni.getStorageSync('savedMediaPaths') || []
						savedPaths = savedPaths.filter(savedPath => savedPath !== path)
						uni.setStorageSync('savedMediaPaths', savedPaths)

						// 更新 mediaItems
						this.mediaItems = this.mediaItems.filter(item => item.path !== path)
						this.selectCount = this.mediaItems.filter(item => item.selected).length
					},
					fail: (error) => {
						console.error('文件删除失败:', error)
						uni.showToast({
							title: '删除失败',
							icon: 'none'
						})
					}
				})
			},


			showFullscreen(index) {
				this.currentFullscreenIndex = index
				this.isFullscreenVisible = true
			},
			closeFullscreen() {
				this.isFullscreenVisible = false
			},
			handleTouchStart(event) {
				if (event.touches.length === 2) {
					this.touchStartTime = Date.now()
					this.touchStartDistance = this.getTouchDistance(event.touches)
				}
			},
			handleTouchMove(event) {
				if (event.touches.length === 2) {
					const currentDistance = this.getTouchDistance(event.touches)
					const distanceDiff = currentDistance - this.touchStartDistance

					if (Math.abs(distanceDiff) > 10 && !this.isZooming) {
						this.isZooming = true
						if (distanceDiff > 0 && this.columns > 1) {
							this.columns--
						} else if (distanceDiff < 0 && this.columns < 5) {
							this.columns++
						}
					}
				}
			},
			handleTouchEnd() {
				this.isZooming = false
			},
			getTouchDistance(touches) {
				const dx = touches[0].clientX - touches[1].clientX
				const dy = touches[0].clientY - touches[1].clientY
				return Math.sqrt(dx * dx + dy * dy)
			}
		}
	}
</script>

<style lang="scss">
	.container {
		display: flex;
		flex-direction: column;
		min-height: 100vh;
		background-color: #FFFDF6; // 奶白色背景
	}

	.header {
		padding: 20px;
		background-color: #FFFDF6;
		text-align: center;
		border-bottom: 1px solid #E0E0E0;
	}

	.title {
		font-size: 24px;
		font-weight: bold;
		color: #333;
	}

	.content {
		flex: 1;
		padding: 2px;
	}

	.gallery-grid {
		display: grid;
		grid-template-columns: repeat(var(--columns), 1fr);
		gap: 2px;
	}

	.gallery-item {
		aspect-ratio: 1;
		overflow: hidden;
		border-radius: 4px;
		position: relative;
	}

	.gallery-item image,
	.gallery-item video {
		width: 100%;
		height: 100%;
		object-fit: cover;
	}

	.top {
		position: fixed;
		top: 30rpx;
		left: 0;
		// padding-right: 20px;
		z-index: 999;
		width: 100vw;
		display: flex;
		justify-content: space-between;
		align-items: center;

		.back {

			height: 50rpx;
			text-align: center;
			line-height: 50rpx;
			margin: 0 20rpx;
			color: #5a5a5a;

			// background-color: #dddddd87; /* 半透明灰色背景 */
			text {
				font-size: 28px;

			}

		}

		.select {
			width: 100rpx;
			height: 50rpx;
			border-radius: 25rpx;
			margin: 0 20rpx;
			text-align: center;
			line-height: 50rpx;
			color: #5a5a5a;
			font-size: 26rpx;
			background-color: #cccccc60;
			/* 半透明灰色背景 */

		}
	}

	.buttom {
		position: fixed;
		bottom: 0;
		left: 0;
		right: 0;
		// padding-right: 20px;
		z-index: 9;
		width: 100vw;
		height: 100rpx;
		background-color: white;
		display: flex;
		justify-content: space-between;
		align-items: center;

		.allselect,
		.delete {
			margin: 0 30px;
			font-size: 27px;

			text {
				font-size: 27px;
			}
		}
	}

	.mask {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		z-index: 100;
		background-color: rgba(255, 253, 247, 0.5);

		/* 半透明灰色背景 */
		.mask-circle {
			position: absolute;
			top: 10px;
			right: 10px;

			text {
				font-size: 20px;
			}
		}
	}
</style>