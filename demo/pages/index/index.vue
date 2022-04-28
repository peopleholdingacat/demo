<template>
	<view class="content">
		<button  style="width:100%;" @click="deuto">跳转到物品检测</button>
		 <button @click="upload" style="width:100%;">{{att}}</button>
		  <button @click="check" style="width:100%;">检测</button>
		<view ><video :src="url_video" ></video></view>
		<view style="width:100%;">
	
			<view v-for="(row,index) in data" :key="row.file"  style="width:100%;"><view @click="show(row.src)" style="width:100%;border:1px solid #ddd">{{row.file}}</view></view>
		</view>
		<image :src="src_photo" style="width: 100%;height: 800rpx;"></image>
		<view class="font12">
		该人物所在视频:{{text}}
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello',
				data:[],
				url_list:[],
				src:"",
				url_video:"",
				att:"上传",
				timeid:"",
				id:"",
				src_photo:"",
				text:"请上传图片",
			}
		},
		onLoad() {
			setInterval(this.get_list,2000)
		},
		methods: {
			deuto(){
				uni.navigateTo({
				url:"../choose/choose"
				})
			},
			get_thing(){
						var that=this
						console.log(this.id)
						uni.request({
							url:"https://cn-cd-dx-6.natfrp.cloud:30496/get_jindu/",
							data:{
								id:this.id
							},
							success(res) {
								
								if(res.data.att=="true"){
									//that.btnLoading=true
									console.log("sadasdasdsaads")
									that.att="finish"
									console.log(that.src)
									that.url_video=that.src
									clearTimeout(that.timeid)
									
									
								}
								else if(res.data.att=="going"){
									that.att="going"
								}
								else if(res.data.att=="false"){
									that.att="false"
								}
								else if(res.data.att=="no id"){
									that.att="请刷新页面，没有发现id"
									clearTimeout(this.timeid)
								}
							}
					})
			},
			show(url){
				var that=this
				that.url_video=url
			},
				get_list(){
					var that=this
					uni.request({
						url:"https://cn-cd-dx-6.natfrp.cloud:30496/get_list1/",
						data:{
							
						},
						success(res) {
							console.log(res.data)
							var get_list=res.data.split(",");
							var url_list=[];
	
							for(var i in get_list){
								if(get_list[i]!=""){
								url_list.push({"file":get_list[i],
								"src":"https://cn-cd-dx-6.natfrp.cloud:30496/Uploadmp4/update_mzy/"+get_list[i],
								})
								}
							}
							//console.log(that.data)
							that.data=url_list
							//console.log(that.data)
						},
						fail() {
						}
					})
				},
		check(){
			var that=this;
		    uni.chooseFile({
				  type: 'image',
		        success: (chooseImageRes) => {
		            const tempFilePaths = chooseImageRes.tempFiles[0].path;
					console.log(tempFilePaths)
					that.src_photo=tempFilePaths
					 const tempFilePaths1 = chooseImageRes.tempFilePaths;
		            uni.uploadFile({
		                url: 'https://cn-cd-dx-6.natfrp.cloud:30496/check', 
		                filePath: tempFilePaths1[0],
		                name: 'file',
							
						formData:{
							"Access-Control-Allow-Origin": "*"
						},
		                success(uploadFileRes) {
							var str=""
		                    console.log(uploadFileRes.data);
							for(var i in eval(uploadFileRes.data)){
								console.log(eval(uploadFileRes.data)[i])
								str+=eval(uploadFileRes.data)[i]+".mp4 "
							}
							if(str==""){
								str="没有该人物出现的视频"
							}
							that.text=str;
							
		                }
		            });i
		        }
		    });
		},
		upload(){
			var that=this;
		    uni.chooseFile({
		        success: (chooseImageRes) => {
		            const tempFilePaths = chooseImageRes.tempFilePaths;
					console.log(tempFilePaths)
					that.att="正在上传"
		            uni.uploadFile({
		                url: 'https://cn-cd-dx-6.natfrp.cloud:30496/get_save', 
		                filePath: tempFilePaths[0],
		                name: 'file',
							
						formData:{
							"Access-Control-Allow-Origin": "*"
						},
		                success: (uploadFileRes) => {
		                    console.log(uploadFileRes.data);
							var json=JSON.parse(uploadFileRes.data)
							that.src=json.path;
							console.log(that.src)
							that.att="上传"
							that.timeid=setInterval(
							this.get_thing,
							1500
							)
							that.id=json.task
		                }
		            });
		        }
		    });
		},
	}}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
	
.div1{ border:2px; 
		 margin-top:5px;
		 
}
.font12{width:200px; text-align:left; font-size:20px;}
</style>
