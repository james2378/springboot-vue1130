<template>
	<div class="main-content">
		<!-- 列表页 -->
		<template v-if="showFlag">
			<el-form class="center-form-pv" :style='{"margin":"0 0 20px"}' :inline="true" :model="searchForm">
				<el-row :style='{"flexWrap":"wrap","justifyContent":"center","display":"flex"}' >
					<div :style='{"margin":"0 10px 0 0","display":"inline-block"}'>
						<label :style='{"margin":"0 10px 0 0","color":"#333","display":"inline-block","lineHeight":"40px","fontSize":"14px","fontWeight":"500","height":"40px"}' class="item-label">车场编号</label>
						<el-input v-model="searchForm.chechangbianhao" placeholder="车场编号" clearable></el-input>
					</div>
					<div :style='{"margin":"0 10px 0 0","display":"inline-block"}'>
						<label :style='{"margin":"0 10px 0 0","color":"#333","display":"inline-block","lineHeight":"40px","fontSize":"14px","fontWeight":"500","height":"40px"}' class="item-label">预约时长</label>
						<el-input v-model="searchForm.yuyueshizhangstart" placeholder="最小预约时长" clearable></el-input>
					</div>
					<div :style='{"margin":"0 10px 0 0","display":"inline-block"}' :label="'至'">
						<el-input v-model="searchForm.yuyueshizhangend" placeholder="最大预约时长" clearable></el-input>
					</div>
					<div :style='{"margin":"0 10px 0 0","display":"inline-block"}'>
						<label :style='{"margin":"0 10px 0 0","color":"#333","display":"inline-block","lineHeight":"40px","fontSize":"14px","fontWeight":"500","height":"40px"}' class="item-label">车牌号</label>
						<el-input v-model="searchForm.chepaihao" placeholder="车牌号" clearable></el-input>
					</div>
					<el-button :style='{"border":"1px solid #999","cursor":"pointer","padding":"0 24px","boxShadow":"1px 2px 4px #ccc","outline":"none","color":"#333","borderRadius":"4px","background":"linear-gradient(150deg, rgba(187,194,208,1) 0%, rgba(247,249,251,1) 50%, rgba(179,187,203,1) 100%)","width":"80px","fontSize":"14px","height":"40px"}' type="success" @click="search()">查询</el-button>
				</el-row>

				<el-row :style='{"margin":"20px 0","display":"flex"}'>
					<el-button :style='{"border":"1px solid #999","cursor":"pointer","padding":"0 12px","boxShadow":"inset 0px 0px 32px 0px #eee","margin":"0 10px 0 0","color":"#000","minWidth":"80px","outline":"none","borderRadius":"4px","background":"linear-gradient(320deg, rgba(187,194,208,1) 0%, rgba(247,249,251,1) 50%, rgba(179,187,203,1) 100%)","width":"auto","fontSize":"14px","height":"36px"}' v-if="isAuth('cheweiyuyue','新增')" type="success" @click="addOrUpdateHandler()">新增</el-button>
					<el-button :style='{"border":"1px solid #eb8787","cursor":"pointer","padding":"0 12px","boxShadow":"inset 0px 0px 32px 0px #f0d2d2","margin":"0 10px 0 0","color":"#c00","minWidth":"80px","outline":"none","borderRadius":"4px","background":"linear-gradient(240deg, rgba(255,168,153,1) 0%, rgba(247,249,251,1) 50%, rgba(249,156,141,1) 100%)","width":"auto","fontSize":"14px","height":"36px"}' v-if="isAuth('cheweiyuyue','删除')" :disabled="dataListSelections.length <= 0" type="danger" @click="deleteHandler()">删除</el-button>




					<el-button :style='{"border":"1px solid #60e669","cursor":"pointer","padding":"0 12px","boxShadow":"inset 0px 0px 32px 0px #d2f0d4","margin":"0 10px 0 0","color":"#06c614","minWidth":"80px","outline":"none","borderRadius":"4px","background":"linear-gradient(240deg, rgba(212,254,215,1) 0%, rgba(249,252,254,1) 50%, rgba(212,254,215,1) 100%)","width":"auto","fontSize":"14px","height":"36px"}' v-if="isAuth('cheweiyuyue','营业额统计')" type="warning" @click="chartDialog1()">营业额统计</el-button>
				</el-row>
			</el-form>
			
			<!-- <div> -->
				<el-table class="tables"
					:stripe='true'
					:style='{"padding":"0","borderColor":"#bbb #999 #999 #999","margin":"0 0 20px","borderRadius":"0px","borderWidth":"4px 0 1px 1px","background":"rgba(255,255,255,.6)","width":"100%","borderStyle":"solid"}' 
					v-if="isAuth('cheweiyuyue','查看')"
					:data="dataList"
					v-loading="dataListLoading"
				@selection-change="selectionChangeHandler">
					<el-table-column :resizable='true' type="selection" align="center" width="50"></el-table-column>
					<el-table-column :resizable='true' :sortable='false' label="索引" type="index" width="50" />
					<el-table-column :resizable='true' :sortable='false'  
						prop="yuyuebianhao"
					label="预约编号">
						<template slot-scope="scope">
							{{scope.row.yuyuebianhao}}
						</template>
					</el-table-column>
					<el-table-column :resizable='true' :sortable='false'  
						prop="chechangbianhao"
					label="车场编号">
						<template slot-scope="scope">
							{{scope.row.chechangbianhao}}
						</template>
					</el-table-column>
					<el-table-column :resizable='true' :sortable='false'  
						prop="chechangmingcheng"
					label="车场名称">
						<template slot-scope="scope">
							{{scope.row.chechangmingcheng}}
						</template>
					</el-table-column>
					<el-table-column :resizable='true' :sortable='false' prop="tupian" width="200" label="图片">
						<template slot-scope="scope">
							<div v-if="scope.row.tupian">
								<img v-if="scope.row.tupian.substring(0,4)=='http'" :src="scope.row.tupian.split(',')[0]" width="100" height="100">
								<img v-else :src="$base.url+scope.row.tupian.split(',')[0]" width="100" height="100">
							</div>
							<div v-else>无图片</div>
						</template>
					</el-table-column>
					<el-table-column :resizable='true' :sortable='false'  
						prop="weizhi"
					label="位置">
						<template slot-scope="scope">
							{{scope.row.weizhi}}
						</template>
					</el-table-column>
					<el-table-column :resizable='true' :sortable='false'  
						prop="xiaoshijiage"
					label="小时价格">
						<template slot-scope="scope">
							{{scope.row.xiaoshijiage}}
						</template>
					</el-table-column>
					<el-table-column :resizable='true' :sortable='false'  
						prop="yuyueshijian"
					label="预约时间">
						<template slot-scope="scope">
							{{scope.row.yuyueshijian}}
						</template>
					</el-table-column>
					<el-table-column :resizable='true' :sortable='false'  
						prop="yuyueshizhang"
					label="预约时长">
						<template slot-scope="scope">
							{{scope.row.yuyueshizhang}}
						</template>
					</el-table-column>
					<el-table-column :resizable='true' :sortable='false'  
						prop="zongfeiyong"
					label="总费用">
						<template slot-scope="scope">
							{{scope.row.zongfeiyong}}
						</template>
					</el-table-column>
					<el-table-column :resizable='true' :sortable='false'  
						prop="zhanghao"
					label="账号">
						<template slot-scope="scope">
							{{scope.row.zhanghao}}
						</template>
					</el-table-column>
					<el-table-column :resizable='true' :sortable='false'  
						prop="nicheng"
					label="昵称">
						<template slot-scope="scope">
							{{scope.row.nicheng}}
						</template>
					</el-table-column>
					<el-table-column :resizable='true' :sortable='false'  
						prop="chepaihao"
					label="车牌号">
						<template slot-scope="scope">
							{{scope.row.chepaihao}}
						</template>
					</el-table-column>
					<el-table-column :resizable='true' :sortable='false' prop="ispay" label="是否支付">
						<template slot-scope="scope">
							<span style="margin-right:10px">{{scope.row.ispay=='已支付'?'已支付':'未支付'}}</span>
							<el-button v-if="scope.row.ispay!='已支付' && isAuth('cheweiyuyue','支付') " type="text" size="small" @click="payHandler(scope.row)">支付</el-button>
						</template>
					</el-table-column>
					<el-table-column width="300" label="操作">
						<template slot-scope="scope">
							<el-button :style='{"border":"1px solid #7fbeef","cursor":"pointer","padding":"0 16px","margin":"3px 6px 3px 0","outline":"none","color":"#1e98f5","borderRadius":"4px","background":"linear-gradient(150deg, rgba(218,239,255,1) 0%, rgba(247,249,251,1) 50%, rgba(214,237,255,1) 100%)","width":"auto","fontSize":"14px","height":"32px"}' v-if=" isAuth('cheweiyuyue','查看')" type="success" size="mini" @click="addOrUpdateHandler(scope.row.id,'info')">详情</el-button>
							<el-button :style='{"border":"1px solid #60e669","cursor":"pointer","padding":"0 16px","margin":"3px 6px 3px 0","outline":"none","color":"#3CB371","borderRadius":"4px","background":"linear-gradient(240deg, rgba(212,254,215,1) 0%, rgba(249,252,254,1) 50%, rgba(212,254,215,1) 100%)","width":"auto","fontSize":"14px","height":"32px"}' v-if=" isAuth('cheweiyuyue','修改')" type="primary" size="mini" @click="addOrUpdateHandler(scope.row.id)">修改</el-button>





							<el-button :style='{"border":"1px solid #eb8787","cursor":"pointer","padding":"0 16px","margin":"3px 6px 3px 0","outline":"none","color":"#c00","borderRadius":"4px","background":"linear-gradient(240deg, rgba(255,168,153,1) 0%, rgba(247,249,251,1) 50%, rgba(249,156,141,1) 100%)","width":"auto","fontSize":"14px","height":"32px"}' v-if="isAuth('cheweiyuyue','删除') " type="danger" size="mini" @click="deleteHandler(scope.row.id)">删除</el-button>
						</template>
					</el-table-column>
				</el-table>
				<el-pagination
					@size-change="sizeChangeHandle"
					@current-change="currentChangeHandle"
					:current-page="pageIndex"
					background
					:page-sizes="[10, 20, 30, 50]"
					:page-size="pageSize"
					:layout="layouts.join()"
					:total="totalPage"
					prev-text="<"
					next-text=">"
					:hide-on-single-page="true"
					:style='{"width":"100%","padding":"0","margin":"20px 0 0","whiteSpace":"nowrap","color":"#eee","fontWeight":"500"}'
				></el-pagination>
			<!-- </div> -->
		</template>
		
		<!-- 添加/修改页面  将父组件的search方法传递给子组件-->
		<add-or-update v-if="addOrUpdateFlag" :parent="this" ref="addOrUpdate"></add-or-update>





		<el-dialog
		  title="营业额统计"
		  :visible.sync="chartVisiable1"
		  width="800">
			<div id="zongfeiyongChart1" style="width:100%;height:600px;"></div>
		  <span slot="footer" class="dialog-footer">
			<el-button @click="chartDialog1">返回</el-button>
		  </span>
		</el-dialog>
	</div>
</template>

<script>
import * as echarts from 'echarts'
import axios from 'axios'
import AddOrUpdate from "./add-or-update";
export default {
  data() {
    return {
      searchForm: {
        key: ""
      },
      form:{},
      dataList: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      dataListSelections: [],
      showFlag: true,
      sfshVisiable: false,
      shForm: {},
      chartVisiable: false,
      chartVisiable1: false,
      chartVisiable2: false,
      chartVisiable3: false,
      chartVisiable4: false,
      chartVisiable5: false,
      addOrUpdateFlag:false,
      layouts: ["total","prev","pager","next","sizes","jumper"],


    };
  },
  created() {
    this.init();
    this.getDataList();
    this.contentStyleChange()
  },
  mounted() {
  },
  filters: {
    htmlfilter: function (val) {
      return val.replace(/<[^>]*>/g).replace(/undefined/g,'');
    }
  },
  components: {
    AddOrUpdate,
  },
  methods: {

    contentStyleChange() {
      this.contentPageStyleChange()
    },
    // 分页
    contentPageStyleChange(){
      let arr = []

      // if(this.contents.pageTotal) arr.push('total')
      // if(this.contents.pageSizes) arr.push('sizes')
      // if(this.contents.pagePrevNext){
      //   arr.push('prev')
      //   if(this.contents.pagePager) arr.push('pager')
      //   arr.push('next')
      // }
      // if(this.contents.pageJumper) arr.push('jumper')
      // this.layouts = arr.join()
      // this.contents.pageEachNum = 10
    },

    payHandler(row){
      this.$storage.set('paytable','cheweiyuyue');
      this.$storage.set('payObject',row);
      this.$router.push('pay');
    },


//统计接口
    chartDialog1() {
      this.chartVisiable1 = !this.chartVisiable1;
      this.$nextTick(()=>{

        var zongfeiyongChart1 = echarts.init(document.getElementById("zongfeiyongChart1"),'macarons');
        this.$http({
            url: `cheweiyuyue/value/chechangmingcheng/zongfeiyong/月`,
            method: "get",
        }).then(({ data }) => {
            if (data && data.code === 0) {
                let res = data.data;
                let xAxis = [];
                let yAxis = [];
                let pArray = []
                for(let i=0;i<res.length;i++){
                    xAxis.push(res[i].chechangmingcheng);
                    yAxis.push(parseFloat((res[i].total)));
                    pArray.push({
                        value: parseFloat((res[i].total)),
                        name: res[i].chechangmingcheng
                    })
                }
                var option = {};
                option = {
                    title: {
                        text: '营业额统计',
                        left: 'center'
                    },
                    tooltip: {
                      trigger: 'item',
                      formatter: '{b} : {c}'
                    },
                    xAxis: {
                        type: 'category',
                        data: xAxis
                    },
                    yAxis: {
                        type: 'value'
                    },
                    series: [{
                        data: yAxis,
                        type: 'bar'
                    }]
                };
                // 使用刚指定的配置项和数据显示图表。
                zongfeiyongChart1.setOption(option);
                  //根据窗口的大小变动图表
                window.onresize = function() {
                    zongfeiyongChart1.resize();
                };
            }
        });
      })
    },





    init () {
    },
    search() {
      this.pageIndex = 1;
      this.getDataList();
    },

    // 获取数据列表
    getDataList() {
      this.dataListLoading = true;
      let params = {
        page: this.pageIndex,
        limit: this.pageSize,
        sort: 'id',
        order: 'desc',
      }
           if(this.searchForm.chechangbianhao!='' && this.searchForm.chechangbianhao!=undefined){
            params['chechangbianhao'] = '%' + this.searchForm.chechangbianhao + '%'
          }
           if(this.searchForm.yuyueshizhangstart!='' && this.searchForm.yuyueshizhangstart!=undefined ){
            params['yuyueshizhangstart'] = this.searchForm.yuyueshizhangstart
          }
          if(this.searchForm.yuyueshizhangend!='' && this.searchForm.yuyueshizhangend!=undefined){
            params['yuyueshizhangend'] = this.searchForm.yuyueshizhangend
          }
           if(this.searchForm.chepaihao!='' && this.searchForm.chepaihao!=undefined){
            params['chepaihao'] = '%' + this.searchForm.chepaihao + '%'
          }
      this.$http({
        url: "cheweiyuyue/page",
        method: "get",
        params: params
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.data.list;
          this.totalPage = data.data.total;
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    // 每页数
    sizeChangeHandle(val) {
      this.pageSize = val;
      this.pageIndex = 1;
      this.getDataList();
    },
    // 当前页
    currentChangeHandle(val) {
      this.pageIndex = val;
      this.getDataList();
    },
    // 多选
    selectionChangeHandler(val) {
      this.dataListSelections = val;
    },
    // 添加/修改
    addOrUpdateHandler(id,type) {
      this.showFlag = false;
      this.addOrUpdateFlag = true;
      this.crossAddOrUpdateFlag = false;
      if(type!='info'){
        type = 'else';
      }
      this.$nextTick(() => {
        this.$refs.addOrUpdate.init(id,type);
      });
    },
    // 下载
    download(file){
      window.open(`${file}`)
    },
    // 删除
    deleteHandler(id) {
      var ids = id
        ? [Number(id)]
        : this.dataListSelections.map(item => {
            return Number(item.id);
          });
      this.$confirm(`确定进行[${id ? "删除" : "批量删除"}]操作?`, "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        this.$http({
          url: "cheweiyuyue/delete",
          method: "post",
          data: ids
        }).then(({ data }) => {
          if (data && data.code === 0) {
            this.$message({
              message: "操作成功",
              type: "success",
              duration: 1500,
              onClose: () => {
                this.search();
              }
            });
          } else {
            this.$message.error(data.msg);
          }
        });
      });
    },


  }

};
</script>
<style lang="scss" scoped>
	
	.center-form-pv {
	  .el-date-editor.el-input {
	    width: auto;
	  }
	}
	
	.el-input {
	  width: auto;
	}
	
	// form
	.center-form-pv .el-input /deep/ .el-input__inner {
				border: 1px solid #999;
				border-radius: 4px;
				padding: 0 12px;
				box-shadow: 0 0 0px rgba(64, 158, 255, .5);
				outline: none;
				color: #333;
				background: linear-gradient(150deg, rgba(187,194,208,1) 0%, rgba(247,249,251,1) 50%, rgba(179,187,203,1) 100%);
				width: 178px;
				font-size: 14px;
				height: 41px;
			}
	
	.center-form-pv .el-select /deep/ .el-input__inner {
				border: 1px solid #999;
				border-radius: 4px;
				padding: 0 10px;
				box-shadow: 0 0 0px rgba(64, 158, 255, .5);
				outline: none;
				color: #333;
				background: linear-gradient(150deg, rgba(187,194,208,1) 0%, rgba(247,249,251,1) 50%, rgba(179,187,203,1) 100%);
				width: 178px;
				font-size: 14px;
				height: 41px;
			}
	
	.center-form-pv .el-date-editor /deep/ .el-input__inner {
				border: 1px solid #999;
				border-radius: 4px;
				padding: 0 10px 0 30px;
				box-shadow: 0 0 0px rgba(64, 158, 255, .5);
				outline: none;
				color: #333;
				background: linear-gradient(150deg, rgba(187,194,208,1) 0%, rgba(247,249,251,1) 50%, rgba(179,187,203,1) 100%);
				width: 178px;
				font-size: 14px;
				height: 41px;
			}
	
	// table
	.el-table /deep/ .el-table__header-wrapper thead {
				color: #999;
				font-weight: 500;
				width: 100%;
			}
	
	.el-table /deep/ .el-table__header-wrapper thead tr {
				background: #fff;
			}
	
	.el-table /deep/ .el-table__header-wrapper thead tr th {
				padding: 10px;
				color: #000;
				background: linear-gradient(60deg, rgba(187,194,208,1) 0%, rgba(247,249,251,1) 50%, rgba(179,187,203,1) 100%);
				border-color: #999;
				border-width: 0 2px 1px 0;
				border-style: solid;
				text-align: left;
			}

	.el-table /deep/ .el-table__header-wrapper thead tr th .cell {
				padding: 0 10px;
				word-wrap: normal;
				word-break: break-all;
				white-space: normal;
				font-weight: bold;
				display: inline-block;
				vertical-align: middle;
				width: 100%;
				line-height: 24px;
				position: relative;
				text-overflow: ellipsis;
			}

	
	.el-table /deep/ .el-table__body-wrapper tbody {
				border: 0;
				width: 100%;
			}

	.el-table /deep/ .el-table__body-wrapper tbody tr {
				border: 0;
				background: none;
			}
	
	.el-table /deep/ .el-table__body-wrapper tbody tr td {
				padding: 12px 0;
				color: #666;
				background: none;
				border-color: #999;
				border-width: 0 2px 1px 0;
				border-style: solid;
				text-align: left;
			}
	
		.el-table /deep/ .el-table__body-wrapper tbody tr.el-table__row--striped td {
		background: rgba(200,200,200,.1);
	}
		
	.el-table /deep/ .el-table__body-wrapper tbody tr:hover td {
				padding: 12px 0;
				color: #666;
				background: rgba(88,131,205,.2);
				border-color: #999;
				border-width: 0 2px 1px 0;
				border-style: solid;
				text-align: left;
			}
	
	.el-table /deep/ .el-table__body-wrapper tbody tr td {
				padding: 12px 0;
				color: #666;
				background: none;
				border-color: #999;
				border-width: 0 2px 1px 0;
				border-style: solid;
				text-align: left;
			}

	.el-table /deep/ .el-table__body-wrapper tbody tr td .cell {
				padding: 0 10px;
				overflow: hidden;
				word-break: break-all;
				white-space: normal;
				line-height: 24px;
				text-overflow: ellipsis;
			}
	
	// pagination
	.main-content .el-pagination /deep/ .el-pagination__total {
				margin: 0 10px 0 0;
				color: #333;
				font-weight: 400;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .btn-prev {
				border: none;
				border-radius: 2px;
				padding: 0;
				margin: 0 5px;
				color: #666;
				background: linear-gradient(150deg, rgba(187,194,208,1) 0%, rgba(247,249,251,1) 50%, rgba(179,187,203,1) 100%);
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				min-width: 35px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .btn-next {
				border: none;
				border-radius: 2px;
				padding: 0;
				margin: 0 5px;
				color: #666;
				background: linear-gradient(240deg, rgba(187,194,208,1) 0%, rgba(247,249,251,1) 50%, rgba(179,187,203,1) 100%);
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				min-width: 35px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .btn-prev:disabled {
				border: none;
				cursor: not-allowed;
				border-radius: 2px;
				padding: 0;
				margin: 0 5px;
				color: #333;
				background: linear-gradient(150deg, rgba(187,194,208,1) 0%, rgba(247,249,251,1) 50%, rgba(179,187,203,1) 100%);
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .btn-next:disabled {
				border: none;
				cursor: not-allowed;
				border-radius: 2px;
				padding: 0;
				margin: 0 5px;
				color: #333;
				background: linear-gradient(150deg, rgba(187,194,208,1) 0%, rgba(247,249,251,1) 50%, rgba(179,187,203,1) 100%);
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				height: 28px;
			}

	.main-content .el-pagination /deep/ .el-pager {
				padding: 0;
				margin: 0;
				display: inline-block;
				vertical-align: top;
			}

	.main-content .el-pagination /deep/ .el-pager .number {
				cursor: pointer;
				padding: 0 4px;
				margin: 0 5px;
				color: #333;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				border-radius: 2px;
				background: radial-gradient(circle, rgba(255,255,255,1) 0%, rgba(179,187,203,1) 100%);
				text-align: center;
				min-width: 30px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .el-pager .number:hover {
				cursor: pointer;
				padding: 0 4px;
				margin: 0 5px;
				color: #333;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				border-radius: 2px;
				background: radial-gradient(circle, rgba(255,255,255,1) 0%, rgba(90,133,207,1) 100%);
				text-align: center;
				min-width: 30px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .el-pager .number.active {
				cursor: default;
				padding: 0 4px;
				margin: 0 5px;
				color: #333;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				border-radius: 2px;
				background: radial-gradient(circle, rgba(255,255,255,1) 0%, rgba(90,133,207,1) 100%);
				text-align: center;
				min-width: 30px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__sizes {
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__sizes .el-input {
				margin: 0 5px;
				width: 100px;
				position: relative;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__sizes .el-input .el-input__inner {
				border: 0px solid #DCDFE6;
				cursor: pointer;
				padding: 0 25px 0 8px;
				color: #333;
				display: inline-block;
				font-size: 13px;
				line-height: 28px;
				border-radius: 3px;
				outline: 0;
				background: linear-gradient(150deg, rgba(187,194,208,1) 0%, rgba(247,249,251,1) 50%, rgba(179,187,203,1) 100%);
				width: 100%;
				text-align: center;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__sizes .el-input span.el-input__suffix {
				top: 0;
				position: absolute;
				right: 0;
				height: 100%;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__sizes .el-input .el-input__suffix .el-select__caret {
				cursor: pointer;
				color: #333;
				width: 25px;
				font-size: 14px;
				line-height: 28px;
				text-align: center;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__jump {
				margin: 0 0 0 24px;
				color: #333;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__jump .el-input {
				border-radius: 3px;
				padding: 0 2px;
				margin: 0 2px;
				display: inline-block;
				width: 50px;
				font-size: 14px;
				line-height: 18px;
				position: relative;
				text-align: center;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__jump .el-input .el-input__inner {
				border: 0px solid #DCDFE6;
				cursor: pointer;
				padding: 0 3px;
				color: #333;
				display: inline-block;
				font-size: 14px;
				line-height: 28px;
				border-radius: 3px;
				outline: 0;
				background: linear-gradient(240deg, rgba(187,194,208,1) 0%, rgba(247,249,251,1) 50%, rgba(179,187,203,1) 100%);
				width: 100%;
				text-align: center;
				height: 28px;
			}
</style>
