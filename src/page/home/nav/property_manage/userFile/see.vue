<template>
    <el-dialog title="住房信息" :visible.sync="msg" :before-close="handleClose">
        <el-table
        class="c-table"
        :data="tableData"
        border
        highlight-current-row
        ref="multipleTable"
        style="width: 100%">

        <el-table-column label="住房"  :show-overflow-tooltip="true" align="center">
          <template slot-scope="scope">{{ scope.row.roomLocation }}</template>
        </el-table-column>

        <!-- <el-table-column label="手机号"  :show-overflow-tooltip="true" align="center">
          <template slot-scope="scope">{{ scope.row.roomLocation }}</template>
        </el-table-column> -->

        <el-table-column label="身份"  :show-overflow-tooltip="true" align="center">
          <template slot-scope="scope">{{scope.row.relationship | relationship }}</template>
        </el-table-column>

        <el-table-column label="认证时间"  :show-overflow-tooltip="true" align="center">
          <template slot-scope="scope">{{ scope.row.auditTime | time('yyyy-MM-dd') }}</template>
        </el-table-column>
      </el-table>
    </el-dialog>
</template>

<script>
export default {
  name: "ChargeSee",
  data() {
    return {
        tableData:[]
    }
  },
  props: ["msg","data"],
  created(){
      this.getListData();
  },
  methods:{
      handleClose() {
        this.$emit("upsee", false );
      },
      getListData() {
        let obj = {userId: this.data.userId, communityId: this.$store.getters.communityId };
        this.$xttp
          .post('/room/query-by-user',obj)
          .then(res => {
            if(!res.errorCode) {
              this.tableData = res.data;
            }
          })
      }
  }
}
</script>

<style lang="scss" scoped>
    .c-list {
        margin-bottom: 15px;
        .c-label {
            display: inline-block;
            width: 100px;
            text-align:right;
        }
        .c-content {
            display: inline-block;
            margin-left: 15px;
            font-weight: bold;
        }
    }
</style>
