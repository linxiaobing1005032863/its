<template>
      <el-dialog :title="titleFont" :visible.sync="msg" :before-close="handleClose">
        <!-- <div class="c-search"> -->
          <el-form :inline="true" :rules="rules" :model="form" class="demo-form-inline">
            <el-row :gutter="20" type="flex" class="row-bg" justify="space-around">
              <el-col :span="10">
                <el-form-item label="日期：" prop="date">
                  <el-date-picker v-model="form.date" type="date" :disabled="editDate" value-format="yyyy-MM-dd" format="yyyy-MM-dd" placeholder="请选择日期">
                  </el-date-picker>
                </el-form-item>
              </el-col>
              <el-col :span="10">
                <el-form-item label="岗位：" prop="postCode">
                  <el-select v-model="form.postCode" @change="changePostCode()" placeholder="岗位" :disabled="disabledPost">
                    <el-option v-for="item in postOptions" :key="item.key" :label="item.name" :value="item.key">
                    </el-option>
                  </el-select>
                </el-form-item>
              </el-col>
            </el-row>
            <!-- <el-form-item label="" prop="date">
              <el-date-picker
                v-model="form.date"
                type="date"
                :disabled="editDate"
                value-format="yyyy-MM-dd"
                format="yyyy-MM-dd"
                placeholder="请选择日期">
              </el-date-picker>
            </el-form-item>

            <el-form-item label="岗位：" prop="postCode">
              <el-select v-model="form.postCode" @change="changePostCode()" placeholder="岗位" :disabled="disabledPost">
                <el-option v-for="item in postOptions" :key="item.key" :label="item.name" :value="item.key">
                </el-option>
              </el-select>
            </el-form-item> -->

            <hr style="height:1px;border:none;border-top:1px dashed #cecece;" />

            <el-form-item label="员工：" prop="empl" class="c-must" style="display:block;margin-top:25px;">
              <!-- <el-select v-model="form.empl" placeholder="请选择员工" @change="changEmpl" :disabled="disabledPost">
                <el-option v-for="(item , indexs) in emplData" :key="item.userId" :label="item.userName" :value="indexs">
                </el-option>
              </el-select> -->
              <el-select v-model="form.empl" placeholder="请选择员工" @change="changEmpl" :disabled="disabledPost">
                <el-option v-for="item in emplData" :key="item.userId" :label="item.userName" :value="item.userId">
                </el-option>
              </el-select>
            </el-form-item>
            <el-form-item v-if="isSee" label="班次：" prop="class" class="c-must" style="display:block;margin-top:25px;float:left;">
              <el-radio-group v-model="form.class">
                <el-radio @change="changeRadio" :checked="checked" :label="item.name" :key="item.name" v-for="(item) in classData" border></el-radio>
                <!-- <el-radio @change="changeRadio" :checked="checked" :label="item.name" :value="item.id" :key="item.name" v-for="(item) in classData" border></el-radio> -->
              </el-radio-group>
            </el-form-item>
            <el-form-item v-if="isShow">
              <el-row :gutter="24" type="flex" class="row-bg" justify="space-around">
                <el-col :span="8">
                  <el-input placeholder="班次名称" v-model="form.name"></el-input>
                </el-col>
                <el-col :span="8">
                  <el-time-select placeholder="出勤时间" v-model="form.attendTimeStr" :picker-options="{
                    start: '00:00',
                    step: '00:05',
                    end: '24:00'
                    }">
                  </el-time-select>
                </el-col>
                <el-col :span="8">
                  <el-time-select placeholder="退勤时间" v-model="form.offTimeStr" :picker-options="{
                    start: '00:00',
                    step: '00:05',
                    end: '24:00'
                    }">
                  </el-time-select>
                </el-col>
              </el-row>
              <el-row :gutter="20" style="margin-top:20px;">
                <el-col :span="8">
                  <el-input placeholder="出勤地点" v-model="form.attendPlace"></el-input>
                </el-col>
                <el-col :span="8">
                  <el-input placeholder="退勤地点" v-model="form.offPlace"></el-input>
                </el-col>
                <el-col :span="8">
                  <el-input placeholder="任务" v-model="form.task"></el-input>
                </el-col>
              </el-row>
              <!-- <div class="demo-input-suffix">
                <el-input
                  placeholder="班次名称"
                  v-model="form.name">
                </el-input>
                <el-time-select placeholder="出勤时间" v-model="form.attendTimeStr"
                :picker-options="{
                  start: '00:00',
                  step: '00:05',
                  end: '24:00'
                  }">
                </el-time-select>
                <el-time-select placeholder="退勤时间" v-model="form.offTimeStr"
                  :picker-options="{
                    start: '00:00',
                    step: '00:05',
                    end: '24:00'
                  }">
                </el-time-select>
                <div style="min-height:20px;"></div>
                <el-input
                  placeholder="出勤地点"
                  v-model="form.attendPlace">
                </el-input>
                <el-input
                  placeholder="退勤地点"
                  v-model="form.offPlace">
                </el-input>
                <el-input
                  placeholder="任务"
                  v-model="form.task">
                </el-input>      
              </div> -->
            </el-form-item>
           
            <el-form-item :label-width="formLabelWidth" style="margin-top:20px;margin-left:40%;" >
              <el-button @click="handleClose">取 消</el-button>
              <el-button type="primary" @click="save">保存</el-button>
            </el-form-item>
          </el-form>
        <!-- </div>        -->
    </el-dialog>
</template>

<script>
import time from '@/utils/time.js';
export default {
  name: "SchedulAdd",
  data() {
    return {
      formLabelWidth: "120px",
      titleFont:'新增排班',
      isSee: false,
      isShow: false,
      checked: false,
      disabledPost: false,
      disabled1: false,
      editDate: false,
      form: {
        postCode: 'SECURITY',
        date: '',
        empl: '',
        class: '',
        name: '',
        attendTimeStr: '',
        offTimeStr: '',
        task:'',
        attendPlace: '',
        offPlace: '',
        classId:''
      },
      rules: {
        empl: [{ required: true, message: "请选中员工", trigger: "blur" }],
        class: [{ required: true, message: "请选中班次", trigger: "blur" }]
      },
      postOptions: [],
      emplData: [],
      classData: [],
      current: 1 //1 初始 2：添加后 3：编辑后
    };
  },
  props: ["msg","add",'edit'],
  mounted() {},
  methods: {
    handleClose() {
      this.$emit("upup", this.current );
    },
    changEmpl(value){
      this.form.empl = value;
    },
    initPost(){
      let communityId = this.$store.getters.communityId
      this.$xttp.get(`/user/property/${communityId}/post-list`).then(res => {
        if(!res.errorCode) {
          this.postOptions = res.data;
        }
      })
    },
    initEmpl() {
      let communityId = this.$store.getters.communityId
      let postCode = this.form.postCode
      this.$xttp.get(`/user/property/${communityId}/user-list`,{params:{postCode:postCode}})
          .then(res => {
            if(res.success) {
              this.emplData = res.data
             
              
            }
          })
    },
    initClass() {
      let postCode = this.form.postCode;
      let communityId = this.$store.getters.communityId;
      let params = {};
      params['communityId'] = communityId;
      params['postCode'] = postCode;
      this.$xttp.post(`/task/class/page`,params)
      .then(res => {
        if(res.success) {
          if(res.data.total == 0){
            this.isSee = false;
          }
          else{
            this.isSee = true;
            this.classData = res.data.records;
          }
        }
      })
    },
    changeRadio() {
      this.isShow = true;
      let obj = this.classData.find(item => this.form.class == item.name);
      let id = obj.id;
      this.$xttp.get(`task/class/${id}/detail`)
          .then(res => {
            if( res.success) {
              this.form.name = res.data.name;
              this.form.attendTime = res.data.attendTime;
              this.form.attendPlace = res.data.attendPlace;
              this.form.offPlace = res.data.offPlace;
              this.form.offTime = res.data.offTime;
              this.form.task = res.data.task;
              this.form.classId = id;
            }
          }).catch(err => {
            console.log(err)
          })
      
    },
    save() {
      if(this.form.empl == ''){
        this.$message({message:'请选择员工',type:'warning'});
        return;
      }
      if(this.form.class == ''){
        this.$message({message:'请选择班次,如没有班次请先去创建班次',type:'warning'});
        return;
      }
      this.postData();
    },
    postData() {
      // let employeeId = this.form.empl;
      let postCode = this.form.postCode;
      let workDate = this.form.date;
      let userId = this.form.empl;
      // let userId = this.form.userId;
      let communityId = this.$store.getters.communityId;
      let propertyId = localStorage.getItem('propertyId');
      let className = this.form.name;
      let attendTimeStr = this.form.attendTimeStr;
      let attendPlace = this.form.attendPlace;
      let offTimeStr = this.form.offTimeStr;
      let offPlace = this.form.offPlace;
      let task = this.form.task;
      let params = {};
      // params['employeeId'] = employeeId;
      params['classId'] = this.form.classId;
      params['className'] = className;
      params['postCode'] = postCode;
      params['workDate'] = workDate;
      params['userId'] = userId;
      params['communityId'] = communityId;
      params['propertyId'] = propertyId;
      params['attendTimeStr'] = attendTimeStr;
      params['attendPlace'] = attendPlace;
      params['offTimeStr'] = offTimeStr;
      params['offPlace'] = offPlace;
      params['task'] = task;
      
      let msg = this.add ? "编辑" : "添加";
      let uri = this.add
        ? "/task/schedule/edit"
        : "/task/schedule/add";
      this.$xttp
        .post(uri, params)
        .then(res => {
          if (res.errorCode === 0) {
            this.$message({
              message: msg + "排班成功",
              type: "success"
            });
            this.current = 2;
            this.handleClose();
            this.$emit('reload');
          } else {
            this.$message({ message: res.data.errorMsg, type: "error" });
          }
        })
        .catch(err => {
          console.log(err);
        });
    },
    changePostCode(){
      this.form.empl = '';
      this.initEmpl();
      this.initClass();
      this.isShow = false;
    }
  },
  created() {
    
    if(this.add) {
      // 判定此时组件为 编辑
      let id = this.add.id;
      // this.form = this.add;
      this.titleFont = "编辑排班";
      let url = `/task/schedule/${id}/detail`;
      this.$xttp.get(url).then(res => {
        if(res.success) {
          this.isSee = true;
          this.isShow = true;
          let records = res.data;

          this.form.postCode = records.postCode;
          if(records.postCode == 'CLEANER'){
            this.form.postCode = '保洁'
          } 
          if(records.postCode == 'SECURITY'){
            this.form.postCode = '保安'
          }
          if(records.postCode == 'MANAGER'){
            this.form.postCode = '物业管理员'
          } 
          if(records.postCode == 'SERVICEMAN'){
            this.form.postCode = '维修工'
          }
          if(records.postCode == 'SUPPORTSTAFF'){
            this.form.postCode = '客服人员'
          }
          this.initEmpl();

          this.form.empl = records.userName;
          this.form.emplId = records.userId;
          this.form.name = records.className;
          this.form.offPlace = records.offPlace;
          this.form.offTimeStr = records.offTimeStr;
          this.form.attendTimeStr = records.attendTimeStr;
          this.form.attendPlace = records.attendPlace;
          this.form.task = records.task;
          this.form.remark = records.remark;
          this.form.class = this.add.className;
          // this.initPost();
          // this.initClass();
          
          this.form.date = time.timestampToTime(records.workDate);
          this.checked = true;
          this.disabled1 = true;
          this.disabledPost = true;
          this.editDate = true;
        }
      })
    }else{
      this.initPost();
      this.disabled1 = false;
      this.initEmpl();
      this.initClass();
    }
  }
};
</script>

<style lang="scss" scoped>
.el-radio+.el-radio{
  margin-left: 0;
  padding: 10px 5px;
  min-width: 100px;
  display: inline-block;
  margin-top: 20px;
}
.el-input{
  width: 200px;
}
.el-radio-group{
}
.el-radio.is-bordered+.el-radio.is-bordered{
  margin-right: 10px;
}
.el-radio.is-bordered{
  height: 40px;
}
.el-radio.is-bordered[data-v-e77d2842]{
  margin-right: 10px;
  margin-top: 0px;
  margin-bottom: 10px;
}
  
</style>
