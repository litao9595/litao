<template>
  <div>
    <div class="top-box"><span @click="preservationBtn">保存</span></div>
    <div class="title-box">资格证书<span><i>{{industryList.length}}</i>/30</span></div>
    <p class="tips-text">请选择证书</p>
    <div class="selct-list">
      <van-collapse v-model="activeNames">
        <van-collapse-item :name="i" class="border-bottom" v-for="(item,i) in list" v-if="item.level==1">
          <div class="accordion-box" slot="title">{{item.credentialTypeName}}<span v-if="false">1</span></div>
          <ul class="li-content">
            <li @click.stop="clickChoice(itm.id,i)" :class="itm.isState?'active':''" v-for="(itm,k) in list2" v-if="item.id==itm.pid">{{itm.credentialTypeName}}</li>
          </ul>
        </van-collapse-item>
      </van-collapse>
    </div>
  </div>
</template>

<script>
import {config} from "../../router/httpconfig";
export default {
    name: "zigezhengshu.vue",
  data(){
      return{
        activeNames: ['-1'],
        list:[],
        list1:[],//一级目录
        list2:[],//二级目录
        industryList:[],//已选证书
        appId:sessionStorage.appId,
        formData:[],
      }
  },
  created(){
      this.dataInitialization();
  },
  methods:{
    preservationBtn(){
      //保存资格证书
      for(let i=0; i<this.industryList.length; i++){
        let _obj={};
        _obj.credentialTypeName=this.industryList[i].credentialTypeName;
        _obj.comCredentialsTypeId=this.industryList[i].id;
        _obj.pid=this.industryList[i].pid;
        this.formData.push(_obj);
      }
      let _this=this;
      let _json=JSON.stringify(_this.formData);
      $.ajax({
        url:config.saveRsCredentialse+'?appId='+this.appId+'&userType=0',
        type:'post',
        contentType: 'application/json',
        data:_json,
        success:(res)=>{
          // console.log(res)
          this.$emit('certificate',false)
        }
      })
    },
    dataInitialization(){
      let _this=this;
      $.ajax({
        url:config.getCredentialsType+'?appId='+this.appId,
        type:'post',
        success:(res)=>{
          _this.list=res.data;
          for(let i=0; i<res.data.length; i++){
            if(res.data[i].level==1){
              res.data[i].mu=0;
              _this.list1.push(res.data[i])
            }
            if(res.data[i].level==2){
              res.data[i].isState=false;
              _this.list2.push(res.data[i])
            }
          }
        }
      })
    },

    clickChoice(_id,index){
      this.activeNames=[index];
      if(this.industryList.length>29){
        this.$toast({
          message:'所选行业不能超过30个',
          position:'center'
        });
        //大于30个
        for(let i=0; i<this.industryList.length; i++){
          if(_id==this.industryList[i].id){
            let _mu = this.industryList.indexOf(this.industryList[i]);
            this.industryList.splice(_mu,1);
          }
        }
        for(let k=0; k<this.list2.length; k++){
          if(_id==this.list2[k].id){
            this.list2[k].isState=false;
          }
        }
      }else{
        //小于30个
        for(let i=0; i<this.list2.length; i++){
          if(_id==this.list2[i].id){
            this.list2[i].isState=!this.list2[i].isState;
            if(this.list2[i].isState){
              this.industryList.push(this.list2[i])
            }else{
              let _mu = this.industryList.indexOf(this.list2[i]);
              this.industryList.splice(_mu,1);
            }
          }
        }
      }
    },

  },
}
</script>

<style scoped>
.top-box{
  text-align: right;
  font-size: 0.25rem;
  color: #45bdd1;
  padding: 0.3rem;
}
.title-box{
  font-size: 0.47rem;
  color: #222222;
  font-weight: bold;
  overflow: hidden;
  line-height: 0.5rem;
  padding: 0rem 0.3rem;
}
.title-box span{
  display: block;
  float: right;
  font-size: 0.22rem;
}
.title-box span i{
  color: #45bdd1;
  font-style: normal;
}
.tips-text{
  padding: 0rem 0.3rem;
  font-size: 0.3rem;
  color: #666666;
  margin-top: 0.3rem;
}
.accordion-box{
  overflow: hidden;
}
.accordion-box span{
  font-size: 14px;
  display: block;
  color: #fff;
  text-align: center;
  line-height: 20px;
  float: right;
  width: 20px;
  height: 20px;
  background: #45bdd1;
  border-radius: 20px;
  overflow: hidden;
}
.li-content{
  overflow: hidden;
}
.li-content li{
  float: left;
  font-size: 0.22rem;
  color: #949595;
  line-height: 0.5rem;
  height: 0.5rem;
  background: #f5f6f6;
  padding: 0rem 0.2rem;
  overflow: hidden;
  border-radius: 0.1rem;
  -webkit-border-radius: 0.1rem;
  -moz-border-radius: 0.1rem;
  margin-right: 0.15rem;
  margin-bottom: 0.15rem;
  box-sizing: border-box;
}
.li-content .active{
  border: solid 1px #45bdd1;
  color: #45bdd1;
  background: #fff;
}
.border-bottom{border-bottom: solid 1px #e9e9e9}
</style>
