<template>
  <div id="transfer">
  			<!-- <Dialog></Dialog> -->
		  	<el-row :gutter="20">
			  <el-col :span="12"><div class="grid-content bg-purple">
			  	<!-- <img src="../../../src/assets/images/common/trans1.png"  > -->
			  </div></el-col>
			  <el-col :span="12"><div class="grid-content bg-purple">
			  		<!-- <img src="../../../src/assets/images/common/trans2.png"  > -->
			  </div></el-col>
			
			</el-row>
	  		<el-container class='edu'>
				  <el-header class='title' style='height:33px'>转换额度
			  </el-header>
		  <el-main>
		  		<el-row :gutter="20" style='margin-bottom:10px'>
				  <el-col :span="3"><div class="grid-content zh">转换额度</div></el-col>
				  <el-col :span="6"><div class="grid-content"><input type="" v-model='shu' name="" onkeyup="this.value=this.value.replace(/\D/g,'')"></div></el-col>
				  <el-col :span="15"><div class="grid-content">
				  	<el-row :gutter="20" class='huan'>
					  <el-col :span="3"   v-for='(item,index) in list'><div class="grid-content bg-purple lala">
					  	<div ref='xuan' @click="select(item,index)">{{item}}</div>
					  </div></el-col>
					</el-row>
				  </div></el-col>
				</el-row>
		  		
		  		<el-row :gutter="20">
				  <el-col :span="3"><div class="grid-content bg-purple">
				  	 <div class="zhuan">
				  	 	转换方式
				  	 </div>
				  </div></el-col>
				  <el-col :span="3"><div class="grid-content bg-purple">
				  	 <div class="zhuan1">
				  	 	<el-radio v-model="radio" label="0">AG转彩票</el-radio>
				  	 </div>
				  </div></el-col>
				  <el-col :span="3"><div class="grid-content ">
				  	 <div class="zhuan1">
				  	 	<el-radio v-model="radio" label="1">彩票转AG</el-radio>
				  	 </div>
				  </div></el-col>
				</el-row>
				<el-row>
				  <el-col :span="24"><div class="grid-content ">
				  			<div class="conmit" >
				  				
				  				<div @click.stop='submit()'>提交</div>
				  				<div @click='reset()'>重置</div>
				  			</div>

				  </div></el-col>
				</el-row>
		  </el-main>
		  
		</el-container>
		<div class="foot">
		  	 <p>汇款信息提交说明：</p>
		  	 <p>(1)请按表格填写准确的汇款转账信息,确认提交后相关财务人员会即时为您查询入款情况</p>
		  	 <!-- <p>(2)请您在转账金额后面加个尾数，例如：转账金额 1000.66或1000.88等，以便相关财务人员更快确认您的转账金额并充值！</p> -->
		  	 <p>(2)如有任何疑问，您可以联系在线客服，在线客服为您提供365天×24小时不间断的友善和专业客服咨询服务！</p>
		  </div>
  </div>
</template>
<script>
import { Loading } from 'element-ui';
	// import Dialog from './dialog'
  export default {
    data () {
      return {
        radio: '0',
        selec:false,
        shu:'',
        balance:sessionStorage.im_money,
        list:[100,500,1000,5000,10000,50000]
      };
    }, 
    methods: {
      select(item,index){
      	this.$emit('child-money', "change1");
      	this.shu=item;
      	this.$refs.xuan.map( item=>{
      			item.className=''
      	})
      	this.$refs.xuan[index].className='active'
      },
      reset(){
      	this.shu='';
      	this.$refs.xuan.map( item=>{
      			item.className=''
      	})
      },
      setmoney(){
		      this.$emit('child-money','change');
		  },
      submit(){
	      	if(this.shu<=0||this.shu==''){
		        this.$swal({
		          text: '请输入正确的转换金额', 
		          type: "error",
		          timer: 1200, 
		        });
	      		return 
	      	}else{

	      		let oid_info=sessionStorage.getItem('im_token');
		      	 let params={};
		      	 params.oid=oid_info;
		      	 params.transfer_io=this.radio;
		      	 params.amount=this.shu;
		      	 let loadingInstance = Loading.service({background:'rgba(0, 0, 0, 0.5)'});
		      	 this.$http.post('/aginfo/agQuotaConversion',JSON.stringify(params)).then(res => {
		      	 	 if(res.data.msg==4001){
				        this.$swal({
				          text: '用户登录失效,请您重新登陆尝试', 
				          type: "error",
				          timer: 1200, 
				        });
		      	 	 	 this.$router.push({
			                path: '/home'
			              })
		      	 	 }
		      	 	if(res.data.msg==7001){ 
				        this.$swal({
				          text: res.data.info, 
				          type: "error",
				          timer: 1200, 
				        });
		      	 	}else if(res.data.msg==2006){
		    			loadingInstance.close();
		      	 		this.reset();
		      	 		if(this.radio==1){
					        this.$swal({
					          text: '彩票成功转入AG'+res.data.amount+'元', 
					          type: "success",
					          timer: 1200, 
					        });
					        this.$emit('agSuccess', 'agSuccess');
		      	 		
		      	 		}else if(this.radio==0){
		      	 			loadingInstance.close();
					        this.$swal({
					          text: 'AG成功转入彩票'+res.data.amount+'元', 
					          type: "success",
					          timer: 1200, 
					        });
					        this.$emit('agSuccess', 'agSuccess');
					       
		      	 		}
		      	 		this.setmoney();
		      	 		// sessionStorage.setItem('im_money', JSON.stringify(this.balance)) 
		      	 		this.shu='';
		      	 	}
		      	 })
	      	}
		 
      }

    }
  }
</script>
<style>
.foot{
	text-align: left;
}
.grid-content img{
	width: 100%;
}
.edu{
	border: 1px solid #e6e6f0;
	border-radius: 5px ;
}
.title{
	text-align: left;
	color:#333333;
	font-weight: bold;

	height: 33px;
	background: #f8f9fb;
	border-bottom: 1px solid #e6e6f0;
}
.lala{
	cursor:pointer;
}
.grid-content .conmit{
	border: none;
	padding: 50px 0;
}
.grid-content .conmit div{
	width: 120px;
	color: #fff;
	display: inline-block;
	border-radius: 60px 0 0 60px ;
	cursor: pointer;
}
.grid-content .conmit div:nth-child(2){
	border-radius: 0 60px  60px 0 ;
}
.grid-content .zhuan{
	border: none;
}
.huan .grid-content div{
	height: 35px;
	line-height: 35px;
	width: 100%;
	border: 1px solid #e6e6f0;
	border-radius: 5px;
}
.zh{
	height: 38px;
	line-height: 38px;
}
.grid-content input{
	width: 100%;
	border: 1px solid #e6e6f0;
	outline: none;
	text-align: center;
	height: 33px;
}
.grid-content  .zhuan1{
	text-align: left;
	border: none;
}
</style>
