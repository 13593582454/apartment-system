<template>
  <div class="s-index">
    <div class="main-title">学生信息管理</div>
    <div class="main select">
      <div class="sub-title clearfix">
        <span class="pull-left">学生信息查询</span>
        <el-button type="primary" class="pull-right relative t--7" @click="$router.push('/addStudents')">添加学生</el-button>
      </div>
      <el-row :gutter="10">
        <el-col :span="8">
          <div class="label">学生姓名</div>
          <el-input v-model="sname" placeholder="请输入学生姓名"></el-input>
        </el-col>
        <el-col :span="8">
          <div class="label">学号</div>
          <el-input v-model="sno" placeholder="请输入学号"></el-input>
        </el-col>
        <el-col :span="8">
          <div class="label">性别</div>
          <el-select v-model="sex" placeholder="请选择">
            <el-option label="全部" value=""></el-option>
            <el-option label="男" value="男"></el-option>
            <el-option label="女" value="女"></el-option>
          </el-select>
        </el-col>
      </el-row>
      <el-row :gutter="10">
        <el-col :span="8">
          <div class="label">学院</div>
          <el-select v-model="college" placeholder="请选择">
            <el-option label="全部" value=""></el-option>
            <el-option label="软件学院" value="软件学院"></el-option>
            <el-option label="计算机科学与技术学院" value="计算机科学与技术学院"></el-option>
            <el-option label="艺术学院" value="艺术学院"></el-option>
            <el-option label="电子工程学院" value="电子工程学院"></el-option>
            <el-option label="生命科学学院" value="生命科学学院"></el-option>
          </el-select>
        </el-col>
        <el-col :span="8">
          <div class="label">手机号</div>
          <el-input v-model="phone" placeholder="请输入手机号"></el-input>
        </el-col>
      </el-row>
      <el-row :gutter="10">
        <el-col :span="8">
          <div class="label">&nbsp;</div>
          <el-button type="primary" class="w-100" @click="select">查询</el-button>
        </el-col>
      </el-row>
      <div class="dash-line"></div>
      <div class="result-num">共查询到{{total}}条结果</div>
      <el-table
        :data="tableData"
        border
        stripe
        style="width: 100%">
        <el-table-column
          align="center"
          prop="name"
          label="姓名"
          width="100">
        </el-table-column>
        <el-table-column
          align="center"
          prop="sex"
          label="性别"
          width="80">
        </el-table-column>
        <el-table-column
          align="center"
          prop="sno"
          width="120"
          label="学号">
        </el-table-column>
        <el-table-column
          align="center"
          prop="college"
          width="180"
          label="学院">
        </el-table-column>
        <el-table-column
          align="center"
          prop="nativePlace"
          label="籍贯">
        </el-table-column>
        <el-table-column
          align="center"
          prop="idNumber"
          width="180"
          label="身份证号">
        </el-table-column>
        <el-table-column
          align="center"
          prop="phone"
          width="160"
          label="手机号">
        </el-table-column>
        <el-table-column
          align="center"
          width="150"
          label="所在寝室">
          <template slot-scope="scope">
            <span v-if="!scope.row.room_id">未分配寝室</span>
            <span v-else>{{scope.row.apartment}}&nbsp;&nbsp;{{scope.row.roomNo}}</span>
          </template>
        </el-table-column>
        <el-table-column
          align="center"
          width="160"
          label="操作">
          <template slot-scope="scope">
            <span class="click" @click="exitStudent(scope.row.id)">编辑</span>
            <span class="click" @click="showDeleteDialog(scope.row.id)">删除</span>
            <span v-if="!scope.row.room_id" class="click" @click="setRoom(scope.row.id)">分配寝室</span>
            <span v-if="scope.row.room_id" class="click" @click="changeRoom(scope.row)">更换寝室</span>
          </template>
        </el-table-column>
      </el-table>
      <div class="oh mt-10">
        <el-pagination
          @current-change="handleCurrentChange"
          :page-size="10"
          layout="total, prev, pager, next"
          :total="total">
        </el-pagination>
      </div>
      <el-dialog
        :visible.sync="deleteDialog"
        center
        width="30%">
        <div class="dialog-title">确认删除本条数据？</div>
        <span slot="footer" class="dialog-footer">
          <el-button type="primary" @click="makeDelete">确 定</el-button>
          <el-button @click="deleteDialog = false">取 消</el-button>
        </span>
      </el-dialog>
      <el-dialog
        :visible.sync="setRoomDialog"
        center
        class="set-room"
        @close="close"
        width="600px">
        <el-form ref="mes" :model="mes" label-width="100px" class="form">
          <el-form-item label="宿舍楼" prop="apartment" :rules="{
            required: true, message: '请选择宿舍楼', trigger: 'change'
          }">
            <el-select v-model="mes.apartment" placeholder="请选择" @change="change">
              <el-option v-for="(x,index) in apartmentArr" :key="index" :label="x.apartment" :value="x.id"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="房间" prop="roomNo" :rules="{
            required: true, message: '请选择房间', trigger: 'change'
          }">
            <el-select v-model="mes.roomNo" placeholder="请选择">
              <el-option v-for="(x,index) in roomArr" :key="index" :label="x.roomNo" :value="x.id"></el-option>
            </el-select>
          </el-form-item>
        </el-form>
        <span slot="footer" class="dialog-footer">
          <el-button type="primary" @click="makeSet">确 定</el-button>
          <el-button @click="setRoomDialog = false">取 消</el-button>
        </span>
      </el-dialog>
    </div>
  </div>
</template>
<script>
  export default {
    data () {
      return {
        sname: '',
        sno: '',
        sex: '',
        college: '',
        phone: '',
        tableData: [],
        total: 0,
        id: '',
        nowPage: 1,
        deleteDialog: false,
        setRoomDialog: false,
        apartmentArr: [],
        roomArr: [],
        changeVal: '',
        changeFlag: 0,
        mes: {
          apartment: '',
          roomNo: ''
        },
      };
    },
    methods: {
      handleCurrentChange (val) {
        this.nowPage = val;
        this.getResult(val);
      },
      exitStudent (id) {
        this.$router.push('/editStudents/' + id);
      },
      showDeleteDialog (id) {
        this.id = id;
        this.deleteDialog = true;
      },
      setRoom (id) {
        this.changeFlag = 0;
        this.id = id;
        this.setRoomDialog = true;
      },
      changeRoom (val) {
        this.changeFlag = 1;
        this.id = val.id;
        this.mes.apartment = val.apartment_id;
        this.change(val.apartment_id).then(res => {
          this.mes.roomNo = val.room_id;
          this.changeVal = val.room_id;
        });
        this.setRoomDialog = true;
      },
      makeDelete () {
        this.deleteDialog = false;
        this.$post(host + 'deleteStudent', {id: this.id}).then(res => {
          if (res) {
            this.$message.success('删除成功');
            this.getResult(this.nowPage);
          }
        });
      },
      change (val) {
        // 通过apartmentId查出房间号
        return new Promise ((resolve, reject) => {
          this.$post(host + 'get_by_apartmentId', {apartmentId: val}).then(res => {
            this.roomArr = res;
            this.mes.roomNo = '';
            resolve();
          });
        });
      },
      makeSet () {
        this.$refs.mes.validate((valid) => {
          if (valid) {
            if (this.changeFlag == 0) { // 设置寝室
              // 先判断房间是否已满 拿到roomType和t_students_rooms里的roomId出现次数
              this.$post(host + 'isFull', {roomId: this.mes.roomNo}).then(res => {
                if (Array.isArray(res) && res.length >= res[0].roomType) {
                  this.$message.error('该房间已满');
                } else {
                  // 传studentId和roomId
                  this.$post(host + 'setRoom', {studentId: this.id, roomId: this.mes.roomNo}).then(res => {
                    if (res == 1) {
                      this.setRoomDialog = false;
                      this.$message.success('设置成功');
                      this.getResult(this.nowPage);
                    }
                  });
                }
              });
            } else { // 更换寝室
              console.log(this.mes.roomNo);
              if (this.changeVal != this.mes.roomNo) {
                this.$post(host + 'isFull', {roomId: this.mes.roomNo}).then(res => {
                  if (Array.isArray(res) && res.length >= res[0].roomType) {
                    this.$message.error('该房间已满');
                  } else {
                    // 传studentId和roomId
                    this.$post(host + 'changeRoom', {studentId: this.id, roomId: this.mes.roomNo}).then(res => {
                      if (res == 1) {
                        this.setRoomDialog = false;
                        this.$message.success('更换成功');
                        this.getResult(this.nowPage);
                      }
                    });
                  }
                });
              } else {
                this.setRoomDialog = false;
              }
            }
          } else {
            console.log('error submit!!');
            return false;
          }
        });
      },
      close () {
        this.resetForm();
        this.mes.apartment = '';
        this.mes.roomNo = '';
      },
      getResult (val) {
        const params = {
          sname: this.sname,
          sno: this.sno,
          sex: this.sex,
          college: this.college,
          phone: this.phone,
          pageSize: 10,
          pageNo: val
        };
        this.$post(host + 'getStudents', params).then(res => {
          if (res == '啥也没有') {
            this.$message.error('啥也没有');
            this.tableData = [];
          } else {
            this.tableData = res.data;
            this.total = res.total;
          }
        });
      },
      select () {
        this.getResult(1);
      },
      resetForm () {
        this.$refs.mes.resetFields();
      },
    },
    created () {
      if (this.$route.params.select) {
        this.select();
      }
      // 发请求，拿所有宿舍楼名称
      this.$post(host + 'getApartment').then(res => {
        this.apartmentArr = res;
      });
      this.$store.commit('changeActive', '1');
    }
  }
</script>
<style lang="scss">
  .set-room {
    .el-select {
      width: 400px!important;
    }
  }
</style>