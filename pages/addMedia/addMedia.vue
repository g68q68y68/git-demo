<template>
  <view class="container">
<!--   <view class="header">
      <text class="title">添加媒体</text>
    </view> -->
    <view class="content">
      <button class="add-button" @click="chooseMedia">
        <text class="add-icon">+</text>
        <text class="add-text">添加照片</text>
      </button>
      <view v-if="mediaList.length > 0" class="media-list">
        <view v-for="(item, index) in mediaList" :key="index" class="media-item">
          <image  :src="item.path" mode="aspectFill" class="media-preview"></image>
          <!-- <image v-if="item.type === 'image'" :src="item.path" mode="aspectFill" class="media-preview"></image> -->
          <!-- <video v-else-if="item.type === 'video'" :src="item.path" class="media-preview"></video> -->
          <text class="media-name">{{ item.name }}</text>
        </view>
      </view>
    </view>
    <view class="footer">
      <button v-if="mediaList.length !== 0" class="save-button" @click="saveMedia" :disabled="mediaList.length === 0">保存媒体</button>
	  <button v-else="mediaList.length === 0" class="save-button" @click="goBackMain" >返回主页</button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      mediaList: []
    }
  },
  methods: {
    chooseMedia() {
      uni.chooseImage({
        count: 9,
        sourceType: ['album'],
		sizeType: ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都有
        success: (res) => {
          this.mediaList = this.mediaList.concat(res.tempFiles.map(file => ({
            ...file,
            type: file.type
          })))
		  console.log('媒体选择成功:', this.mediaList)
		  // 预览图片
		  		uni.previewImage({
		  			urls: res.tempFilePaths,
		  			longPressActions: {
		  				itemList: ['发送给朋友', '保存图片', '收藏'],
		  				success: function(data) {
		  					// console.log('选中了第' + (data.tapIndex + 1) + '个按钮,第' + (data.index + 1) + '张图片');
		  				},
		  				fail: function(err) {
		  					// console.log(err.errMsg);
		  				}
		  			}
		  		});
		  uni.showToast({
			  title: '媒体选择成功',
			  icon: 'success'
		  })
        },
        fail: (err) => {
          console.error('选择媒体失败:', err)
          uni.showToast({
            title: '选择媒体失败',
            icon: 'none'
          })
        }
      })
    },
    async saveMedia() {
      if (this.mediaList.length === 0) return

      try {
		  let savedFilePaths = [];
        for (let media of this.mediaList) {
          const savedFilePath = await this.saveFile(media.path)
          // console.log(`文件已保存到: ${savedFilePath}`)
		  savedFilePaths.push(savedFilePath);
        }
		
		// 将保存的文件路径存储到本地存储
		let existingPaths = uni.getStorageSync('savedMediaPaths') || [];
		existingPaths = existingPaths.concat(savedFilePaths);
		uni.setStorageSync('savedMediaPaths', existingPaths);
		
        uni.showToast({
          title: '媒体保存成功',
          icon: 'success'
        })
        this.mediaList = []
      } catch (error) {
        // console.error('保存媒体失败:', error)
        uni.showToast({
          title: '保存媒体失败',
          icon: 'none'
        })
      }finally{
		  uni.navigateTo({
			url:"/pages/index/index"
		})
	  }
    },
    saveFile(tempFilePath) {
      return new Promise((resolve, reject) => {
        uni.saveFile({
          tempFilePath: tempFilePath,
          success: (res) => {
            resolve(res.savedFilePath)
          },
          fail: (error) => {
            reject(error)
          }
        })
      })
    },
	goBackMain(){
		uni.navigateBack({
			delta:1
		})
	}
  }
}
</script>

<style lang="scss">

.container {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  // background-color: #f0f0f0;
  background-color: #FFFDF6;
}

.header {
  padding: 20px;
  background-color: #ffffff;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  text-align: center;
}

.title {
  font-size: 24px;
  font-weight: bold;
  color: #333;
}

.content {
  flex: 1;
  padding: 20px;
}

.add-button {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 150px;
  background-color: #ffffff;
  border: 2px dashed #ccc;
  border-radius: 10px;
}

.add-icon {
  font-size: 48px;
  color: #999;
}

.add-text {
  // margin-top: 10px;
  font-size: 16px;
  color: #666;
}

.media-list {
  display: flex;
  flex-wrap: wrap;
  margin-top: 20px;
}

.media-item {
  width: calc(33.33% - 10px);
  margin: 5px;
  background-color: #ffffff;
  border-radius: 5px;
  overflow: hidden;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.media-preview {
  width: 100%;
  height: 100px;
  object-fit: cover;
}

.media-name {
  padding: 5px;
  font-size: 12px;
  color: #666;
  text-align: center;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.footer {
  padding: 20px;
  background-color: #ffffff;
  box-shadow: 0 -2px 4px rgba(0, 0, 0, 0.1);
}

.save-button {
  width: 100%;
  height: 44px;
  line-height: 44px;
  background-color: #007aff;
  color: #ffffff;
  font-size: 16px;
  border-radius: 22px;

  &:disabled {
    background-color: #cccccc;
  }
}
</style>