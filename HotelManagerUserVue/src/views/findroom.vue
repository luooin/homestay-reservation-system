<template>
	<div>
		<el-container class="wrapper">
			<!-- <el-header class="header">找房</el-header> -->
			<el-main class="section">
				<div class="bg-header"></div>
				<el-card class="box-card pb-1" style="opacity: 0.95;">
					<div class="text item" style="margin-bottom: 20px;">
						<el-row>
							<el-col :span="16">
								<p class="text-left"><i class="el-icon-location-outline"></i>高新区农场民宿</p>
							</el-col>
						</el-row>
						<el-form ref="form" :model="form">
							<el-row :gutter="20">
								<el-col :span="12">
									<el-form-item label="入住时间">
										<el-date-picker type="datetime" placeholder="选择日期" v-model="form.indate" style="width: 100%;"></el-date-picker>
									</el-form-item>
								</el-col>
								<el-col :span="12">
									<el-form-item label="离店时间">
										<el-date-picker type="datetime" placeholder="选择日期" v-model="form.leavedate" style="width: 100%;"></el-date-picker>
									</el-form-item>
								</el-col>
							</el-row>
						</el-form>
					</div>
				</el-card>

				<el-row class="mt-1">
					<el-card class="mb-1 ml-1 mr-1">
						<div class="text item">
							<el-button type="primary"  style="font-size: 16px;" class="btn-block" @click="searchBtn"> 查询空余房间</el-button>
						</div>
					</el-card>
					<el-col :span="22" v-for="(tp, index) in listdata" :key="index" :offset="1" class="mb-1">
						<el-card :body-style="{ padding: '0px' }">
							<el-row>
								<el-col :span="6">
									<img :src="tp.coverImage" class="image">
								</el-col>
								<el-col :span="18">
									<div style="padding: 14px;" class="text-left room">
										<!-- :href="tp.detailUrl"  -->
										<p class="room-title">
											{{tp.typeName}}
										</p>
										<p class="room-intro">{{tp.introduce}}</p>
										<p class="room-price">￥{{tp.price}} 起</p>
										<p class="room-comment">“{{tp.feature}}”</p>
									</div>
								</el-col>
							</el-row>
						</el-card>
					</el-col>
				</el-row>
			</el-main>
			<el-footer class="footer">
				<footbar></footbar>
			</el-footer>
			<el-dialog title="提示" :visible.sync="dialogVisible" width="95%">
				<span>您还没有选择完整的时间段！</span>
				<span slot="footer" class="dialog-footer">
					<el-button type="primary" @click="dialogVisible = false"> 确定</el-button>
				</span>
			</el-dialog>
		</el-container>
	</div>
</template>

<script>
	import footbar from "@/components/footbar.vue";
	import store from './../store';
	export default {
		data() {
			return {
				dialogVisible: false,
				form: {
					indate: '',
					leavedate: '',
					currentDate: new Date()
				},
				listdata: {

				},
			}
		},
		components: {
			footbar,
		},
		methods: {
			showroom() {
				console.log(123);
				this.roomVisible = true;
			},
			searchBtn() {
				// console.log(this.changeTimeStr(this.form.indate));
				if (this.form.indate == "" || this.form.leavedate == "") {
					this.dialogVisible = true;
					return;
				}
				this.axios.post("http://localhost:9151/user/listRoom", {
						"inTime": this.changeTimeStr(this.form.indate),
						"leaveTime": this.changeTimeStr(this.form.leavedate)
					})
					.then(res => {
						this.$store.state.searchSet = res.data.data;
						this.$store.state.searchTime.inTime = this.changeTimeStr(this.form.indate);
						this.$store.state.searchTime.leaveTime = this.changeTimeStr(this.form.leavedate);
						this.$router.push("/search");
					})
					.catch(() => {
						console.log("error");
					})

			},
			changeTimeStr(str) {
				str = str.toString();
				str = str.replace(/ GMT.+$/, ''); // Or str = str.substring(0, 24)
				let d = new Date(str);
				let a = [d.getFullYear(), d.getMonth() + 1, d.getDate(), d.getHours(), d.getMinutes(), d.getSeconds()];
				for (var i = 0, len = a.length; i < len; i++) {
					if (a[i] < 10) {
						a[i] = '0' + a[i];
					}
				}
				str = a[0] + '-' + a[1] + '-' + a[2] + ' ' + a[3] + ':' + a[4];
				return str;
			},
			unity() {
				window.open("http://unity.abohelloworld.top/");
			}
		},
		mounted() {
			this.axios.get("http://localhost:9151/user/listTypes")
				.then(res => {
					this.listdata = res.data.data;
					console.log(this.listdata);
				})
				.catch(res => {
					console.log(res);
				})
		}
	}
</script>

<style scoped="scoped">
	.box-card {
		height: 30vh;
		margin: -7rem 1rem 0;
	}

	.time {
		font-size: 13px;
		color: #999;
	}

	.bottom {
		margin-top: 13px;
		line-height: 12px;
	}

	.button {
		padding: 0;
		float: right;
	}

	.image {
		width: 120%;
		display: block;
		padding: 1rem;
		border: 1px solid transparent;
		border-radius: 20px;
	}

	.clearfix:before,
	.clearfix:after {
		display: table;
		content: "";
	}

	.clearfix:after {
		clear: both
	}

	.room p {
		margin-bottom: 0.25rem;
		margin-top: 0;
		margin-left: 4rem;
	}

	.room-title {
		font-size: 1.2rem;
		font-weight: bolder;
		color: #303133;
	}

	.room-intro {
		font-size: 0.9rem;
		color: #606266;
	}

	.room-price {
		font-size: 1.2rem;
		color: #F56C6C;
	}

	.room-comment {
		font-size: 0.8rem;
		color: #909399;
	}

	.black {
		color: #303133;
	}
</style>
