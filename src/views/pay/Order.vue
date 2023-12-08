/**
 * 订单管理 交易订单
 */
<template>
  <div>
    <!-- 面包屑导航 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>学生名单</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 搜索筛选 -->
    <el-form :inline="true" :model="formInline" class="user-search">
      <el-form-item label="搜索：">
        <el-input size="small" v-model="formInline.studentName" placeholder="输入学生姓名"></el-input>
      </el-form-item>
      <el-form-item>
        <el-input size="small" v-model="formInline.studentLevel" placeholder="输入学生等级"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button size="small" type="primary" icon="el-icon-search" @click="search">搜索</el-button>
      </el-form-item>
      <el-form-item class="form-item-left-align">
        <el-button size="small" type="primary" icon="el-icon-plus" @click="addfrom=true">新增</el-button>
      </el-form-item>

    </el-form>
    <!--列表-->
    <el-table size="small" :data="listData" highlight-current-row v-loading="loading" border element-loading-text="拼命加载中" style="width: 100%;">
      <el-table-column align="center" type="index" width="60">
      </el-table-column>
      <el-table-column sortable prop="studentId" label="学生编号" width="250" show-overflow-tooltip>
      </el-table-column>
      <el-table-column sortable prop="studentNumber" label="电话号码" width="220" show-overflow-tooltip>
      </el-table-column>
      <el-table-column sortable prop="studentName" label="学生姓名" width="220" show-overflow-tooltip>
      </el-table-column>
      <el-table-column sortable prop="studentLevel" label="学生等级" width="220" show-overflow-tooltip>
      </el-table-column>
      <el-table-column sortable prop="drillTeacher" label="学生老师" width="220" show-overflow-tooltip>
      </el-table-column>

      <el-table-column align="center" label="操作" min-width="180">
        <template slot-scope="scope">
          <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">修改</el-button>
          <el-button size="mini" type="danger" @click="deleteUser(scope.$index, scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页组件 -->
    <Pagination v-bind:child-msg="pageparm" @callFather="callFather"></Pagination>

    <el-dialog :title="title" :visible.sync="editFormVisible" width="30%" @click="closeDialog">
      <el-form label-width="120px" :model="editForm"  ref="editForm">
        <el-form-item label="学生编号" prop="deptName">
          <el-input size="small" v-model="editForm.studentId" auto-complete="off" placeholder="请输入学生编号"></el-input>
        </el-form-item>
        <el-form-item label="电话号码" prop="deptNo">
          <el-input size="small" v-model="editForm.studentNumber" auto-complete="off" placeholder="请输入电话号码"></el-input>
        </el-form-item>
        <el-form-item label="学生姓名" prop="deptName">
          <el-input size="small" v-model="editForm.studentName" auto-complete="off" placeholder="请输入学生姓名"></el-input>
        </el-form-item>
        <el-form-item label="学生等级" prop="deptNo">
          <el-input size="small" v-model="editForm.studentLevel" auto-complete="off" placeholder="请输入学生等级"></el-input>
        </el-form-item>
        <el-form-item label="学生老师" prop="deptNo">
          <el-input size="small" v-model="editForm.drillTeacher" auto-complete="off" placeholder="请输入学生老师"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button size="small" @click="editFormVisible=false">取消</el-button>
        <el-button size="small" type="primary" :loading="loading" class="title" @click="submitForm('editForm')">保存</el-button>
      </div>
    </el-dialog>


    <el-dialog :title="title" :visible.sync="addfrom" width="30%" @click="closeDialog">
      <el-form label-width="120px" :model="editForm"  ref="editForm">
        <el-form-item label="学生编号" prop="deptName">
          <el-input size="small" v-model="editForm.studentId" auto-complete="off" placeholder="请输入学生编号"></el-input>
        </el-form-item>
        <el-form-item label="电话号码" prop="deptNo">
          <el-input size="small" v-model="editForm.studentNumber" auto-complete="off" placeholder="请输入电话号码"></el-input>
        </el-form-item>
        <el-form-item label="学生姓名" prop="deptName">
          <el-input size="small" v-model="editForm.studentName" auto-complete="off" placeholder="请输入学生姓名"></el-input>
        </el-form-item>
        <el-form-item label="学生等级" prop="deptNo">
          <el-input size="small" v-model="editForm.studentLevel" auto-complete="off" placeholder="请输入学生等级"></el-input>
        </el-form-item>
        <el-form-item label="学生老师" prop="deptNo">
          <el-input size="small" v-model="editForm.drillTeacher" auto-complete="off" placeholder="请输入学生老师"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button size="small" @click="editFormVisible=false">取消</el-button>
        <el-button size="small" type="primary" :loading="loading" class="title" @click="add('editForm')">保存</el-button>
      </div>
    </el-dialog>
    <!-- 编辑界面 -->
<!--    <el-dialog :title="title" :visible.sync="editFormVisible" width="50%" @click="closeDialog('editForm')">-->
<!--      <el-form label-width="120px" :model="editForm" ref="editForm">-->
<!--        <el-row>-->
<!--          <el-col :span="12">-->
<!--            <el-form-item label="公司编号">-->
<!--              <el-input size="small" v-model="editForm.deptId" auto-complete="off" placeholder="请输入名称" disabled></el-input>-->
<!--            </el-form-item>-->
<!--            <el-form-item label="订单号">-->
<!--              <el-input size="small" v-model="editForm.orderNo" auto-complete="off" placeholder="请输入商户号" disabled></el-input>-->
<!--            </el-form-item>-->
<!--            <el-form-item label="支付方式">-->
<!--              <el-input size="small" v-model="editForm.payType" auto-complete="off" placeholder="请输入商户号" disabled></el-input>-->
<!--            </el-form-item>-->
<!--            <el-form-item label="交易类型">-->
<!--              <el-input size="small" v-model="editForm.transType" auto-complete="off" placeholder="请输入微信子商户" disabled></el-input>-->
<!--            </el-form-item>-->
<!--            <el-form-item label="商品编号">-->
<!--              <el-input size="small" v-model="editForm.goodsNo" auto-complete="off" placeholder="请输入应用ID" disabled></el-input>-->
<!--            </el-form-item>-->
<!--            <el-form-item label="支付金额">-->
<!--              <el-input size="small" v-model="editForm.payAmount" auto-complete="off" placeholder="请输入通知回调" disabled></el-input>-->
<!--            </el-form-item>-->
<!--            <el-form-item label="货道号">-->
<!--              <el-input size="small" v-model="editForm.aisleNo" auto-complete="off" placeholder="请输入加密类型" disabled></el-input>-->
<!--            </el-form-item>-->
<!--            <el-form-item label="买家标识">-->
<!--              <el-input size="small" v-model="editForm.openId" auto-complete="off" placeholder="请输入商户签名密钥" disabled></el-input>-->
<!--            </el-form-item>-->
<!--            <el-form-item label="子商户号">-->
<!--              <el-input size="small" v-model="editForm.subMchId" auto-complete="off" placeholder="请输入支付宝卖家" disabled></el-input>-->
<!--            </el-form-item>-->
<!--          </el-col>-->
<!--          <el-col :span="12">-->
<!--            <el-form-item label="公司名称">-->
<!--              <el-input size="small" v-model="editForm.deptName" auto-complete="off" placeholder="请输入名称" disabled></el-input>-->
<!--            </el-form-item>-->
<!--            <el-form-item label="交易单号">-->
<!--              <el-input size="small" v-model="editForm.transId" auto-complete="off" placeholder="请输入商户号" disabled></el-input>-->
<!--            </el-form-item>-->
<!--            <el-form-item label="子支付方式">-->
<!--              <el-input size="small" v-model="editForm.subPayType" auto-complete="off" placeholder="请输入商户号" disabled></el-input>-->
<!--            </el-form-item>-->
<!--            <el-form-item label="终端编号">-->
<!--              <el-input size="small" v-model="editForm.machineNo" auto-complete="off" placeholder="请输入微信子商户" disabled></el-input>-->
<!--            </el-form-item>-->
<!--            <el-form-item label="商品价格">-->
<!--              <el-input size="small" v-model="editForm.goodsPrice" auto-complete="off" placeholder="请输入应用ID" disabled></el-input>-->
<!--            </el-form-item>-->
<!--            <el-form-item label="商品名称">-->
<!--              <el-input size="small" v-model="editForm.goodsName" auto-complete="off" placeholder="请输入通知回调" disabled></el-input>-->
<!--            </el-form-item>-->
<!--            <el-form-item label="订单状态">-->
<!--              <el-input size="small" v-model="editForm.orderStatus" auto-complete="off" placeholder="请输入加密类型" disabled></el-input>-->
<!--            </el-form-item>-->
<!--            <el-form-item label="商户号">-->
<!--              <el-input size="small" v-model="editForm.mchId" auto-complete="off" placeholder="请输入商户签名密钥" disabled></el-input>-->
<!--            </el-form-item>-->
<!--            <el-form-item label="编辑用户">-->
<!--              <el-input size="small" v-model="editForm.editUser" auto-complete="off" placeholder="请输入支付宝卖家" disabled></el-input>-->
<!--            </el-form-item>-->
<!--          </el-col>-->
<!--        </el-row>-->
<!--        <el-form-item label="备注">-->
<!--          <el-input size="small" v-model="editForm.remark" auto-complete="off" placeholder="请输入微信证书路径" disabled></el-input>-->
<!--        </el-form-item>-->
<!--      </el-form>-->
<!--    </el-dialog>-->
  </div>
</template>

<script>
import { OrderList, OrderRefund, OrderDelete } from '../../api/payMG'
import Pagination from '../../components/Pagination'
import axios from "axios";
export default {
  data() {
    return {
      loading: false, //是显示加载
      editFormVisible: false, //控制编辑页面显示与隐藏
      addfrom:false,
      title: '预览',
      tag:localStorage.getItem('tag'),

      editForm: {
        id: '',
        studentId: '',
        studentNumber: '',
        drillTeacher: "",
        studentLevel: "",
        studentName:"",
        drillNumber:"",
        token: localStorage.getItem('logintoken')
      },
      user:{
        username:"",
        password:"123",
        tag:"2",
      },
      formInline: {
        page: 1,
        limit: 10,
        id: '',
        studentId: '',
        studentNumber: '',
        drillTeacher: "",
        studentLevel: "",
        studentName:"",
        drillNumber:"",
        token: localStorage.getItem('logintoken')
      },
      // 删除部门
      seletedata: {
        ids: '',
        token: localStorage.getItem('logintoken')
      },
      userparm: [], //搜索权限
      listData: [], //用户数据
      // 分页参数
      pageparm: {
        currentPage: 1,
        pageSize: 10,
        total: 10
      }
    }
  },
  // 注册组件
  components: {
    Pagination
  },
  /**
   * 数据发生改变
   */

  /**
   * 创建完毕
   */
  created() {
    this.getdata(this.formInline)
  },

  /**
   * 里面的方法只有被调用才会执行
   */
  methods: {
    // 获取公司列表
    getdata(parameter) {
      this.loading = true



      //OrderList(parameter)
      if(localStorage.getItem('tag')==0){
      axios.post('/api/student/' + parameter.page + '/' + parameter.limit,
        JSON.stringify(this.formInline
        ),
        {
          headers: {
            'Content-Type': 'application/json'
          }
        })
        .then(res => {
          this.loading = false
          if (res.success == false) {
            this.$message({
              type: 'info',
              message: res.msg
            })
          } else {
            this.listData = res.data.records
            // 分页赋值
            this.pageparm.currentPage = this.formInline.page
            this.pageparm.pageSize = this.formInline.limit
            this.pageparm.total = res.data.count
          }
        })
        .catch(err => {
          this.loading = false
          this.$message.error('菜单加载失败，请稍后再试！')
        })
    }
      if(localStorage.getItem('tag')==1){
        this.formInline.drillNumber=localStorage.getItem('id'),
        console.log(JSON.stringify(
          this.formInline
        ))
        axios.post('/api/student/' + parameter.page + '/' + parameter.limit,
          JSON.stringify(
            this.formInline
          ),
          {
            headers: {
              'Content-Type': 'application/json'
            }
          })
          .then(res => {
            console.log(res.data.records)
            this.loading = false
            if (res.success == false) {
              this.$message({
                type: 'info',
                message: res.msg
              })
            } else {
              this.listData = res.data.records
              // 分页赋值
              this.pageparm.currentPage = this.formInline.page
              this.pageparm.pageSize = this.formInline.limit
              this.pageparm.total = res.data.count
            }
          })
          .catch(err => {
            this.loading = false
            this.$message.error('菜单加载失败，请稍后再试！')
          })
      }
      },

    add(editData) {
      this.editForm.drillNumber=localStorage.getItem('id'),
      this.$refs[editData].validate(valid => {
        if (valid) {
          this.loading = true
          axios.post('/api/student/', JSON.stringify(this.editForm), {
            headers: {
              'Content-Type': 'application/json'
            }
          })
            .then(res => {
              this.editFormVisible = false;
              this.loading = false;
              this.user.username=this.editForm.studentName;

              this.adduser();
              if (res.data== true) { // 判断成功的条件
                this.getdata(this.formInline);
                this.closeDialog();
                this.$message({
                  type: 'success',
                  message: '学生信息添加成功！',
                })

              } else {
                this.$message({
                  type: 'info',
                  message: res.msg || '添加失败，请稍后再试！'
                })
              }
            })
            .catch(err => {
              this.getdata(this.formInline);
              this.addfrom = false;
              this.loading = false;
              this.$message.success('学生信息添加成功！');
            })
        } else {
          return false
        }
      })
    },
    adduser(){
      axios.post('/api/user/put', JSON.stringify(this.user), {
        headers: {
          'Content-Type': 'application/json'
        }
      })
        .then(res => {

        })
      console.log("okkkk!")
    },
    // 分页插件事件
    callFather(parm) {
      this.formInline.page = parm.currentPage
      this.formInline.limit = parm.pageSize
      this.getdata(this.formInline)
    },
    // 搜索事件
    search() {
      console.log(this.formInline)
      this.getdata(this.formInline)
    },

    submitForm(editData) {
      this.$refs[editData].validate(valid => {
        if (valid) {
          this.loading = true
          axios.put('/api/student/', JSON.stringify(this.editForm), {
            headers: {
              'Content-Type': 'application/json'
            }
          })
            .then(res => {
              this.editFormVisible = false
              this.loading = false
              if (res) {
                this.getdata(this.formInline)
                this.$message({
                  type: 'success',
                  message: '学生信息保存成功！'
                })
              } else {
                this.$message({
                  type: 'info',
                  message: res.msg
                })
              }
            })
            .catch(err => {
              this.editFormVisible = false
              this.loading = false
              this.$message.error('保存失败，请稍后再试！')
            })
        } else {
          return false
        }
      })
    },
    //显示编辑界面
    handleEdit: function(index, row) {
      this.editFormVisible = true
      this.editForm = row
    },
    // 编辑、增加页面保存方法
    // submitForm(editData) {
    //   this.$refs[editData].validate(valid => {
    //     if (valid) {
    //       ConfigSave(this.editForm)
    //         .then(res => {
    //           this.editFormVisible = false
    //           this.loading = false
    //           if (res.success) {
    //             this.getdata(this.formInline)
    //             this.$message({
    //               type: 'success',
    //               message: '公司保存成功！'
    //             })
    //           } else {
    //             this.$message({
    //               type: 'info',
    //               message: res.msg
    //             })
    //           }
    //         })
    //         .catch(err => {
    //           this.editFormVisible = false
    //           this.loading = false
    //           this.$message.error('支付配置信息保存失败，请稍后再试！')
    //         })
    //     } else {
    //       return false
    //     }
    //   })
    // },
    // 删除公司
    deleteUser(index, row) {
      this.$confirm('确定要删除吗?', '信息', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(() => {
          axios.delete('/api/student/'+row.id)
            .then(res => {
              if (res.data) {
                this.$message({
                  type: 'success',
                  message: '学生信息已删除!'
                })
                this.getdata(this.formInline)
              } else {
                this.$message({
                  type: 'info',
                  message: res.msg
                })
              }
            })
            .catch(err => {
              this.loading = false
              this.$message.error('学生信息删除失败，请稍后再试！')
            })
        })
        .catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          })
        })
    },
    // 关闭编辑、增加弹出框
    closeDialog(formName) {
      this.editFormVisible = false
      this.$refs[formName].resetFields()
    }
  }
}
</script>

<style scoped>
.user-search {
  margin-top: 20px;
}
.userRole {
  width: 100%;
}
</style>




