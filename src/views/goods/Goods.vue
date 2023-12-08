/**
 * 基础菜单 商品管理
 */

<template>
  <div>
    <!-- 面包屑导航 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>请假申请</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 搜索筛选 -->
    <el-form :inline="true" :model="formInline" class="user-search">
      <el-form-item label="">
        <el-input size="small" v-model="formInline.studentName" placeholder="输入学生名"></el-input>
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
        <el-table-column align="center" type="selection" width="60">
        </el-table-column>
        <el-table-column sortable prop="studentName" label="学生姓名" width="300">
        </el-table-column>
        <el-table-column sortable prop="holidayCause" label="请假理由" width="300">
        </el-table-column>
        <el-table-column sortable prop="holidayIdea" label="请假意见" width="300">
        </el-table-column>
        <el-table-column sortable prop="holidayTime" label="请假时间" width="300">

      </el-table-column>

      <el-table-column align="center" label="操作" min-width="300">
        <template slot-scope="scope">
          <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
          <el-button size="mini" type="danger" @click="deleteUser(scope.$index, scope.row)":disabled="tag!=0">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页组件 -->
    <Pagination v-bind:child-msg="pageparm" @callFather="callFather"></Pagination>
    <!-- 编辑界面 -->
    <el-dialog :title="title" :visible.sync="editFormVisible" width="30%" @click="closeDialog">
      <el-form label-width="120px" :model="editForm"  ref="editForm">
        <el-form-item label="学生名称" prop="deptName">
          <el-input size="small" v-model="editForm.studentName" auto-complete="off" placeholder="请输入学生名称":disabled="Formtag.disabledNameInput"></el-input>
        </el-form-item>
        <el-form-item label="请假理由" prop="deptNo">
          <el-input size="small" v-model="editForm.holidayCause" auto-complete="off" placeholder="请输入请假理由":disabled="Formtag.disabledNameCause"></el-input>
        </el-form-item>
        <el-form-item label="请假意见" prop="deptName">
          <el-input size="small" v-model="editForm.holidayIdea" auto-complete="off" placeholder="请输入学生名称":disabled="Formtag.disabledNameIdea"></el-input>
        </el-form-item>
        <el-form-item label="请假时间" prop="deptNo">
          <el-input size="small" v-model="editForm.holidayTime" auto-complete="off" placeholder="请输入请假理由":disabled="Formtag.disabledNameTime"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeDialog">取消</el-button>
        <el-button size="small" type="primary" :loading="loading" class="title" @click="submitForm('editForm')">保存</el-button>
      </div>
    </el-dialog>


    <el-dialog :title="title" :visible.sync="addfrom" width="30%" @click="closeDialog">
      <el-form label-width="120px" :model="editForm"  ref="editForm">
        <el-form-item label="学生名称" prop="deptName">
          <el-input size="small" v-model="editForm.studentName" auto-complete="off" placeholder="请输入学生名称":disabled="tag==2"></el-input>
        </el-form-item>
        <el-form-item label="请假理由" prop="deptNo">
          <el-input size="small" v-model="editForm.holidayCause" auto-complete="off" placeholder="请输入请假理由"></el-input>
        </el-form-item>
        <el-form-item label="请假意见" prop="deptName">
          <el-input size="small" v-model="editForm.holidayIdea" auto-complete="off" placeholder="请输入请假意见"></el-input>
        </el-form-item>
        <el-form-item label="请假时间" prop="deptNo">
          <el-input size="small" v-model="editForm.holidayTime" auto-complete="off" placeholder="请输入请假时间"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button size="small" @click="addfrom=false">取消</el-button>
        <el-button size="small" type="primary" :loading="loading" class="title" @click="add('editForm')">添加</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { deptList, deptSave, deptDelete } from '../../api/userMG'
import Pagination from '../../components/Pagination'
import axios from "axios";
export default {
  data() {
    return {
      tag:localStorage.getItem('tag'),
      nshow: true, //switch开启
      fshow: false, //switch关闭
      loading: false, //是显示加载
      editFormVisible: false, //控制编辑页面显示与隐藏
      title: '添加',
      addfrom: false,
      editForm: {
        studentName:"",
        holidayCause:"",
        holidayIdea:"",
        holidayTime:"",
        id:"",
        studentId:"",
        token: localStorage.getItem('logintoken')
      },
      Formtag:{
        disabledNameInput: false,
        disabledNameCause: false,
        disabledNameIdea: false,
        disabledNameTime: false,

      },
      // rules表单验证
      rules: {
        deptName: [
          { required: true, message: '请输入部门名称', trigger: 'blur' }
        ],
        deptNo: [{ required: true, message: '请输入部门代码', trigger: 'blur' }]
      },
      formInline: {
        page: 1,
        limit: 10,
        varLable: '',
        varName: '',
        token: localStorage.getItem('logintoken'),
        studentName:"",
        holidayCause:"",
        holidayIdea:"",
        holidayTime:"",
      },
      user:{username:""},
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

    this.getdata(this.formInline);

  },
  mounted() {
    const tag = localStorage.getItem('tag')
    if (tag === '1') {
      this.editForm.studentName.prop = 'readonly'
      this.editForm.holidayCause.prop = 'readonly'
      this.editForm.holidayIdea.prop = 'readonly'
      this.editForm.holidayTime.prop = 'readonly'

    } else if (tag === '2') {
      this.editForm.studentName.prop = 'disabled'
      this.editForm.holidayCause.prop = 'disabled'
      this.editForm.holidayIdea.prop = 'disabled'
      this.editForm.holidayTime.prop = 'disabled'
    }
  },
  closeDialog() {
    this.addfrom = false;
    // 清空表单数据
    this.editForm = {
      studentName: '',
      holidayCause: '',
      holidayIdea: '',
      holidayTime: '',
    };
  },
  /**
   * 里面的方法只有被调用才会执行
   */
  methods: {
    // 获取公司列表
    getdata(parameter) {
      this.loading = true
      // // 模拟数据开始
      // let res = {
      //   code: 0,
      //   msg: null,
      //   count: 5,
      //   data: [
      //     {
      //       addUser: null,
      //       editUser: '张三',
      //       addTime: 1521062371000,
      //       editTime: 1526700200000,
      //       deptId: 2,
      //       deptName: '20192354173',
      //       deptNo: '13792354173',
      //       parentId: 1
      //     },
      //     {
      //       addUser: null,
      //       editUser: '李四',
      //       addTime: 1521063247000,
      //       editTime: 1526652291000,
      //       deptId: 3,
      //       deptName: '20192354178',
      //       deptNo: '13792354171',
      //       parentId: 1
      //     },
      //     {
      //       addUser: null,
      //       editUser: '王五',
      //       addTime: 1526349555000,
      //       editTime: 1526349565000,
      //       deptId: 12,
      //       deptName: '20192354179',
      //       deptNo: '13792354172',
      //       parentId: 1
      //     },
      //     {
      //       addUser: null,
      //       editUser: '赵六',
      //       addTime: 1526373178000,
      //       editTime: 1526373178000,
      //       deptId: 13,
      //       deptName: '20192354179',
      //       deptNo: '137923541712',
      //       parentId: 1
      //     },
      //     {
      //       addUser: null,
      //       editUser: '鸡哥',
      //       addTime: 1526453107000,
      //       editTime: 1526453107000,
      //       deptId: 17,
      //       deptName: '20192354170',
      //       deptNo: '13792354172',
      //       parentId: 1
      //     }
      //   ]
      // }
      // this.loading = false
      // this.listData = res.data
      // this.pageparm.currentPage = this.formInline.page
      // this.pageparm.pageSize = this.formInline.limit
      // this.pageparm.total = res.count
      // // 模拟数据结束

      /***
       * 调用接口，注释上面模拟数据 取消下面注释
       */


      // console.log(parameter.pageparm.currentPage);


      if(this.tag==2){

        axios.post('/api/holiday/' + parameter.page + '/' + parameter.limit,
        JSON.stringify({
          studentName: parameter.studentName,
          studentId: localStorage.getItem('id'),
        }),
        {
          headers: {
            'Content-Type': 'application/json'
          }
        }).then(res => {
        // 处理获取到的数据
        this.listData = res.data.records;
        this.pageparm.total=res.data.total;
        this.user.username=res.data.records[0].studentName;
        this.editForm.studentName=res.data.records[0].studentName;
        console.log(this.user)
      }).catch(err => {
        // 处理错误情况
      })}else{
      axios.post('/api/holiday/' + parameter.page + '/' + parameter.limit,
        JSON.stringify({
          studentName: parameter.studentName
        }),
        {
          headers: {
            'Content-Type': 'application/json'
          }
        }).then(res => {
        // 处理获取到的数据
        this.listData = res.data.records;
        this.pageparm.total=res.data.total;
      }).catch(err => {
        // 处理错误情况
      })}

      deptList(parameter)
        .then(res => {
          this.loading = false
          if (res.success == false) {
            this.$message({
              type: 'info',
              message: res.msg
            })
          } else {
            this.listData = res.data
            // 分页赋值
            this.pageparm.currentPage = this.formInline.page
            this.pageparm.pageSize = this.formInline.limit
            this.pageparm.total = res.count
          }
        })
        .catch(err => {
          this.loading = false
          //this.$message.error('菜单加载失败，请稍后再试！')
        })
    },
    // 分页插件事件
    callFather(parm) {
      this.formInline.page = parm.currentPage
      this.formInline.limit = parm.pageSize
      this.getdata(this.formInline)
    },
    // 搜索事件
    search() {
      this.getdata(this.formInline)
    },

    //显示编辑界面
    handleEdit: function(index, row) {
      this.editFormVisible = true
      if (row != undefined && row != 'undefined') {
        this.title = '修改'
        this.editForm.studentName = row.studentName
        this.editForm.holidayCause = row.holidayCause
        this.editForm.holidayIdea = row.holidayIdea
        this.editForm.holidayTime = row.holidayTime
        this.editForm.id=row.id;
        let tag=localStorage.getItem('tag')
        if(tag==2){
          this.Formtag.disabledNameTime=true
          this.Formtag.disabledNameInput=true
          this.Formtag.disabledNameCause=true
          this.Formtag.disabledNameIdea=true
        }else if(tag==1){
          this.Formtag.disabledNameTime=true
          this.Formtag.disabledNameInput=true
          this.Formtag.disabledNameCause=true

        }
        // axios.post('/api/holiday/', JSON.stringify(this.editForm), {
        //   headers: {
        //     'Content-Type': 'application/json'
        //   }
        //   }).then(res => {
        //   // 处理获取到的数据
        //   if(res.data=true)
        //     this.getdata(this.formInline)
        //   else
        //     this.$message.error('修改失败，请稍后再试！')
        // }).catch(err => {
        //   // 处理错误情况
        // })
      }
    },


    // 编辑、增加页面保存方法
    submitForm(editData) {
      this.$refs[editData].validate(valid => {
        if (valid) {
          this.loading = true
          axios.put('/api/holiday', JSON.stringify(this.editForm), {
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
                  message: '请假信息保存成功！'
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

    add(editData) {

      if(this.tag==2) {
        this.editForm.studentId = localStorage.getItem('id');
        console.log(JSON.stringify(this.editForm));
        console.log(JSON.stringify(this.editForm.studentId));
        this.loading = true
        axios.post('/api/holiday', JSON.stringify(this.editForm), {
          headers: {
            'Content-Type': 'application/json'
          }
        })
          .then(res => {
            this.addForm = false
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
      }else if(this.tag!=2){
        this.user.username=this.editForm.studentName;
        axios.post('/api/user', JSON.stringify(this.user), {
          headers: {
            'Content-Type': 'application/json'
          }
        })
          .then(res => {

            if (res.data!=null) {
              console.log(this.user);
              this.editForm.studentId=res.data.id;
              console.log(this.editForm);
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
            this.$message.error('学生信息不存在失败，请稍后再试！')
          })
        axios.post('/api/holiday', JSON.stringify(this.editForm), {
          headers: {
            'Content-Type': 'application/json'
          }
        })
          .then(res => {

            if (res) {
              this.getdata(this.formInline);
              this.addForm=false;
              this.$message({
                type: 'success',
                message: '请假信息保存成功！'
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
            this.$message.error('学生信息不存在失败，请稍后再试！')
          })

      }

            },
    // 删除公司
    deleteUser(index, row) {
      this.$confirm('确定要删除吗?', '信息', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(() => {
          axios.delete('/api/holiday/'+row.id)
            .then(res => {
              if (res.data) {
                this.$message({
                  type: 'success',
                  message: '请假信息已删除!'
                })
                this.getdata(this.formInline)
              } else {
                this.$message({
                  type: 'info',
                  message: '删除失败'
                })
              }
            })
            .catch(err => {
              console.error(err)
              this.$message.error('请假信息删除失败，请稍后再试！')
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
    closeDialog() {
      this.editFormVisible = false
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


