<template>
      <el-dialog :title="titleFont" :visible.sync="msg" :before-close="handleClose">
        <el-form :model="form" :rules="rules" ref="ruleForm" class="demo-form-inline">

            <el-form-item label="房间编号" :label-width="formLabelWidth" prop="name" class="c-must">
            <el-input v-model="form.name"></el-input>
            </el-form-item>

            <el-form-item label="所属楼栋" prop="buildingId" :label-width="formLabelWidth" class="c-must">
              <el-select v-model="form.buildingId" clearable>
                <el-option
                  v-for="item in floorOptions"
                  :key="item.id"
                  :value="item.id"
                  :label="item.name">
                </el-option>
              </el-select>
            </el-form-item>

            <el-form-item label="所在楼层" prop="floorCode" :label-width="formLabelWidth" class="c-must">
              <el-input-number v-model="form.floorCode" :min="-5" :max="100" label="选择所在楼层"></el-input-number>
            </el-form-item>
          <el-form-item label="住房面积" prop="area" :label-width="formLabelWidth">
            <el-input type="number" v-model="form.area"></el-input>
          </el-form-item>

            <el-form-item :label-width="formLabelWidth">
              <el-button @click="handleClose">取 消</el-button>
              <el-button type="primary" @click="save('ruleForm')">保存</el-button>
            </el-form-item>
        </el-form>
    </el-dialog>
</template>

<script>
import { communityId,toName } from '@/biz/community';

export default {
  name: "floorFileForm",
  data() {
    return {
      formLabelWidth: "120px",
      titleFont:'添加房间档案',
      isShow: false,
      form: {
        name: "",
        communityIdShow: toName(this.$store.getters.communityList,this.$store.getters.communityId),
        communityId: this.$store.getters.communityId,
        buildingId: "",
        floorCode: "",
        area:''
      },
      rules: {
        name: [{ required: true, message: "请输入房间编号", trigger: "blur" }],
        buildingId: [{ required: true, message: "请输入楼栋名称", trigger: "blur" }],
        floorCode: [{ required: true, message: "请输入楼层", trigger: "blur" }],
        area:[{ required: true, message: "请输入面积", trigger: "blur" }]
      },
      floorOptions: [],//楼层下拉
      current: 1 ,//1 初始 2：添加后 3：编辑后
    };
  },
  props: ["msg","add"],
  created() {
    this.selectCommunity();
    if(this.add){//判断此时组件为 编辑
      this.form = this.add;
      console.log(this.add);
      this.titleFont = '编辑房间档案';
      this.form.communityIdShow = this.$store.getters.communityName
      this.form.area = (this.add.area/100).toFixed(2);
    }
  },
  mounted() {},
  methods: {
    handleClose() {
      this.$emit("upup", this.current );
    },
    save(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.postData();
        } else {
          return false;
        }
      });
    },
    selectCommunity(){
      let obj = { communityId:this.form.communityId };
      this.$xttp.get(`/community/building/list`,{params:obj})
        .then(res => {
          if(!res.errorCode){
            this.floorOptions = res.data;
          }
        })
    },
    postData() {
      let msg = this.add ? '编辑' : '添加';
      let uri = this.add ? '/community/room/edit' : '/community/room/add';
      // if(this.add){
      //   let id = this.add.id;
      // }
      let name = this.form.name;
      let communityId = this.form.communityId;
      let buildingId = this.form.buildingId;
      let floorCode = this.form.floorCode;
      let area = this.form.area;
      let communityIdShow = this.form.communityIdShow;

      let params = {};
      if(this.add){
        params['id'] = this.add.id;
      }
      params['name'] = name;
      params['communityId'] = communityId;
      params['communityIdShow'] = communityIdShow;
      params['buildingId'] = buildingId;
      params['floorCode'] = floorCode;
      params['area'] = area * 100;
      this.$xttp
        .post( uri, params)
        .then(res => {
          if (res.errorCode === 0) {
            this.$message({
              message: msg + "房间成功",
              type: "success"
            });
            this.current =  3;
            this.handleClose();
            this.$emit('reload');
          }else {
            this.$message({message:res.data.errorMsg,type:'error'});
          }
        })
        .catch(err => {
          console.log(err);
        });
    }
  }
};
</script>

<style lang="scss" scoped>

</style>


