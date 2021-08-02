<!-- 表格 -->
<template>
	<div class="cy-table-wrap">
		<div class="fifter-wrap card" v-if="filter">
				<!-- 表格筛选区域 -->
				<el-form ref="classFilterForm" :model="filterData" :inline="true" class="class-filter-form"
					:size="size">
					<slot name="filter"></slot>
					<el-form-item>
						<el-button :size="size" icon="el-icon-search" type="primary" :loading="loading" @click="reload(1)">查询</el-button>
					</el-form-item>
				</el-form>
		</div>
		<div v-show="!noTable" class="table-wrap card">
			<!-- 表格头部按钮区域 -->
			<div v-if="tableBtn" class="table-sort" flex="align:center justify:between">
				<div class="btn-wrap-left">
					<slot name="btnLeft"></slot>
				</div>
				<div class="btn-wrap-right">
					<slot name="btnRight"></slot>
				</div>
			</div>
			<el-table v-loading="loading" class="cy-table" :data="tableData" :size="size" style="width: 100%;"
				@sort-change="sortChange" :span-method="spanMethod" @selection-change="selectionChange" @current-change="currentChange"
				@row-click="rowClick">
				<slot />
				<div slot="empty" class="tablebox">
					<img src="../../assets/images/no-data.png">
					<p>暂无数据</p>
				</div>
			</el-table>
			<!-- 表格分页 -->
			<el-pagination v-if="paging" class="pagination-wrap" background hide-on-single-page
				:current-page.sync="pageData.page" :page-size="pageData.limit" layout="total, prev, pager, next"
				:total="tableCount" @current-change="pageChange" />
		</div>
	</div>
</template>
<script>
	export default {
		// 二次封装el-table,传入api的function实现自动分页
		name: 'CyTable',
		props: {
			// 请求表格数据接口
			api: {
				required: true,
				type: Function
			},
			filter: {
				type: Boolean,
				default: true
			},
			noTable: {
				type: Boolean,
				default: false
			},
			// 是否展示表格按钮区域
			tableBtn: {
				type: Boolean,
				default: false
			},
			/**
			 * 导出的配置项
			 * api: 导出的接口函数，不传时不显示导出按钮
			 * noPage： 导出是否不包含分页的参数
			 */
			exportConfig: {
				type: Object,
				default: function() {
					return {
						api: null,
						noPage: false
					}
				}
			},
			// 分页参数
			pageData: {
				type: Object,
				default: function() {
					return {
						limit: 10,
						page: 1
					}
				}
			},
			// 筛选参数
			filterData: {
				type: Object,
				default: function() {
					return {}
				}
			},
			// 表格尺寸
			size: {
				type: String,
				default: 'mini'
			},
			// 是否分页
			paging: {
				type: Boolean,
				default: true
			},
			spanMethod: {
				type: Function
			},
		},
		data() {
			return {
				tableCount: 0, // 总条数
				tableData: [], // 表格数据
				loading: false, // loading动画
				loadingDown: false
			}
		},
		activated() {
			!this.noTable && this.init()
		},
		mounted() {
			!this.noTable && this.init()
		},
		methods: {
			init() {
				this.tableData = []
				const data = this.getParams(this.paging)
				this.loading = true
				this.api(data).then(res => {
					this.loading = false
					// 如果有分页
					if (this.paging) {
						this.tableData = res.data.list || []
						this.tableCount = res.data.total || 0
					} else {
						this.tableData = res.data || []
					}
					this.$emit('load-finish', res)
				}).catch((err) => {
					console.error(err)
					this.loading = false
				})
			},
			getParams(e) {
				let data = {
					...this.filterData
				}
				if (e) {
					data = {
						...this.filterData,
						...this.pageData
					}
				}
				return data
			},
			/**
			 * 重新请求 如果需要重新请求使用this.$refs 调用这个方法
			 * 参数为分页的参数，不传为当前刷新
			 */
			reload(e) {
				if (e && !(/(^[1-9]\d*$)/.test(e))) {
					e = 1
				}
				e && (this.pageData.page = e)
				if(!this.noTable) {
					this.init()
				}
			},
			// 导出表格数据
			download() {
				const data = this.getParams(!this.exportConfig.noPage)
				this.loadingDown = true
				this.exportConfig.api(data).then((res) => {
					this.loadingDown = false
				}).catch(() => {
					this.loadingDown = false
				})
			},
			// 以下是对el-table原来的方法再次封装emit出去
			// 多选
			selectionChange(val) {
				this.$emit('selection-change', val)
			},
			// 单选
			currentChange(currentRow, oldCurrentRow) {
				this.$emit('current-change', currentRow, oldCurrentRow)
			},
			rowClick(row, event, column) {
				this.$emit('row-click', row, event, column)
			},
			// 排序
			sortChange({ column, prop, order }) {
				this.$emit('sort-change', column, prop, order)
			},
			// 表格翻页
			pageChange(page) {
				this.pageData.page = page
				this.init()
			}
		}
	}
</script>
<style scoped lang="scss">
	.cy-table-wrap {
		::v-deep .el-select .el-icon-arrow-up:before {
			// content: "\e78f";
			font-size: 12px;
			color: $fontColor9;
		}
	}
	.class-filter-form{
		::v-deep{
			.el-select {
				width: 140px;
			}
			.el-form-item{
				margin-right: 25px;
			}
		}
	}
	.fifter-wrap {
		margin-bottom: 15px;
		position: relative;
		padding-top: 10px;
	}

	.table-wrap {
		padding: 0 0 30px 0;
		transform: translateZ(0);
	}

	.table-sort {
		height: 60px;
		padding: 0 15px;
		
		.el-dropdown-link {
			cursor: pointer;
		}
		.btn-wrap-left {
			margin-right: 40px;
		}
	}

	.pagination-wrap {
		margin-top: 30px;
		text-align: right;
	}
</style>