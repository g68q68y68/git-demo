<template>
	<view v-if="openMask" class="mask" @click="ischange">
		<view class="mask-circle" >
<!-- 			<text v-if="value === 0" class="iconfont icon-xuanzhong"></text>
			<text v-else class="iconfont icon-xuanzhong" style="color: deepskyblue;"></text> -->
			<!-- <text v-if="value === 1" class="iconfont icon-xuanzhong" style="color: deepskyblue;"></text> -->
			<text v-if="isSelect" class="iconfont icon-xuanzhong" style="color: deepskyblue;"></text>
		</view>
	</view>
</template>

<script>
	export default {
		props:{
			isSelect:{
				type:Boolean,
				require:true
			},
			keyValue:{
				type:Number,
				require:true
			},
			openMask:{
				type:Boolean,
				require:true
			},
			allSelect:{
				type:Boolean,
				require:true
			},
			firstCount:{
				type:Number,
				require:true
			},
			secondCount:{
				type:Number,
				require:true
			}
		
		},
		watch:{
			allSelect:{
				handler(value){
					if(this.secondCount === this.firstCount || this.secondCount === 0){
						
						if (value ) {
							this.value = 1
						} else{
							this.value = 0
						}
					}
					
					
				},
				immediate:true
			}
		},
		data() {
			return {
				value:0
			};
		},
		methods:{
			ischange(){
				if(this.value === 0){
					this.value = 1
				}else{
					this.value = 0
				}
				this.$emit('isSelect',this.keyValue)
			}
		},
	}
</script>

<style lang="scss">
.mask{
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		z-index: 100;
		background-color: rgba(255, 253, 247, 0.5); /* 半透明灰色背景 */
		.mask-circle{
			position: absolute;
			top: 10px;
			right: 10px;
			text{
				font-size: 20px;
			}
		}
	}
</style>