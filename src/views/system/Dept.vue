/**
 * 系统管理  公司管理
 */
<template>
  <div>
    <!-- 面包屑导航 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>训练师管理</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 搜索筛选 -->
    <el-form :inline="true" :model="formInline" class="user-search">
      <el-form-item label="搜索：">
        <el-input size="small" v-model="select" placeholder="输入训练师姓名"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button size="small" type="primary" icon="el-icon-search" @click="search">搜索</el-button>
        <el-button size="small" type="primary" icon="el-icon-plus" @click="addForm=true":disabled="tag!=0">添加</el-button>
      </el-form-item>
    </el-form>

    <!--列表-->
    <el-table size="small" :data="listData" highlight-current-row v-loading="loading" border element-loading-text="拼命加载中" style="width: 100%;">
      <el-table-column align="center" type="selection" width="60">
      </el-table-column>
      <el-table-column sortable prop="drillName" label="训练师名称" width="300">
      </el-table-column>
      <el-table-column sortable prop="drillSex" label="训练师性别" width="300">
      </el-table-column>
      <el-table-column sortable prop="drillNumber" label="训练师号码" width="300">
      </el-table-column>
      <el-table-column sortable prop="drillStyle" label="训练师风格" width="300">
      </el-table-column>
      <el-table-column sortable prop="drillNote" label="训练师简介" width="300">
      </el-table-column>

      <el-table-column align="center" label="操作" min-width="300">
        <template slot-scope="scope">
          <el-button size="mini" @click="get(scope.$index, scope.row)":disabled="tag!=0">编辑</el-button>
          <el-button size="mini" type="danger" @click="deleteUser(scope.$index, scope.row)":disabled="tag!=0">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页组件 -->
    <Pagination v-bind:child-msg="pageparm" @callFather="callFather"></Pagination>




    <!-- 编辑界面 -->
    <el-dialog :title="title" :visible.sync="editFormVisible" width="30%" @click="closeDialog">
      <el-form label-width="120px" :model="editForm" :rules="rules" ref="editForm">
        <el-form-item label="训练师名称" prop="deptName">
          <el-input size="small" v-model="editForm.drillName" auto-complete="off" placeholder="请输入训练师名称"></el-input>
        </el-form-item>
        <el-form-item label="训练师性别" prop="deptNo">
          <el-input size="small" v-model="editForm.drillSex" auto-complete="off" placeholder="请输入训练师性别"></el-input>
        </el-form-item>
        <el-form-item label="训练师电话号码" prop="deptNo">
          <el-input size="small" v-model="editForm.drillNumber" auto-complete="off" placeholder="请输入训练师电话号码"></el-input>
        </el-form-item>
        <el-form-item label="训练师风格" prop="deptNo">
          <el-input size="small" v-model="editForm.drillStyle" auto-complete="off" placeholder="请输入训练师风格"></el-input>
        </el-form-item>
        <el-form-item label="训练师简介" prop="deptNo">
          <el-input size="small" v-model="editForm.drillNote" auto-complete="off" placeholder="请输入训练师简介"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeDialog">取消</el-button>
        <el-button size="small" type="primary" :loading="loading" class="title" @click="update()">保存</el-button>
      </div>
    </el-dialog>

    <el-dialog :title="title" :visible.sync="addForm" width="30%" @click="closeDialog">
      <el-form label-width="120px" :model="editForm" :rules="rules" ref="editForm">
        <el-form-item label="训练师名称" prop="deptName">
          <el-input size="small" v-model="editForm.drillName" auto-complete="off" placeholder="请输入训练师名称"></el-input>
        </el-form-item>
        <el-form-item label="训练师性别" prop="deptNo">
          <el-input size="small" v-model="editForm.drillSex" auto-complete="off" placeholder="请输入训练师性别"></el-input>
        </el-form-item>
        <el-form-item label="训练师电话号码" prop="deptNo">
          <el-input size="small" v-model="editForm.drillNumber" auto-complete="off" placeholder="请输入训练师电话号码"></el-input>
        </el-form-item>
        <el-form-item label="训练师风格" prop="deptNo">
          <el-input size="small" v-model="editForm.drillStyle" auto-complete="off" placeholder="请输入训练师风格"></el-input>
        </el-form-item>
        <el-form-item label="训练师简介" prop="deptNo">
          <el-input size="small" v-model="editForm.drillNote" auto-complete="off" placeholder="请输入训练师简介"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeDialog">取消</el-button>
        <el-button size="small" type="primary" :loading="loading" class="title" @click="submitForm('editForm')">保存</el-button>
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
      addForm:false,
      nshow: true, //switch开启
      fshow: false, //switch关闭
      loading: false, //是显示加载
      editFormVisible: false, //控制编辑页面显示与隐藏
      title: '添加',
      editForm: {
        id:"",
        drillName:"",
        drillSex:"",
        drillNumber:"",
        drillStyle:"",
        drillNote:"",
        token: localStorage.getItem('logintoken')
      },
      // rules表单验证

      formInline: {
        page: 1,
        limit: 10,
        varLable: '',
        varName: '',
        token: localStorage.getItem('logintoken')
      },
      // 删除部门
      seletedata: {
        ids: '',
        token: localStorage.getItem('logintoken')
      },
      select:"",
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
      // // 模拟数据
      // let res = {
      //   code: 0,
      //   msg: null,
      //   count: 5,
      //   data: [
      //     {
      //       addUser: null,
      //       editUser: null,
      //       addTime: 1521062371000,
      //       editTime: 1526700200000,
      //       deptId: 2,
      //       deptName: '上海分公司',
      //       deptNo: '1',
      //       parentId: 1
      //     },
      //     {
      //       addUser: null,
      //       editUser: null,
      //       addTime: 1521063247000,
      //       editTime: 1526652291000,
      //       deptId: 3,
      //       deptName: '上海测试',
      //       deptNo: '02',
      //       parentId: 1
      //     },
      //     {
      //       addUser: null,
      //       editUser: null,
      //       addTime: 1526349555000,
      //       editTime: 1526349565000,
      //       deptId: 12,
      //       deptName: '1',
      //       deptNo: '11',
      //       parentId: 1
      //     },
      //     {
      //       addUser: null,
      //       editUser: null,
      //       addTime: 1526373178000,
      //       editTime: 1526373178000,
      //       deptId: 13,
      //       deptName: '5',
      //       deptNo: '5',
      //       parentId: 1
      //     },
      //     {
      //       addUser: null,
      //       editUser: null,
      //       addTime: 1526453107000,
      //       editTime: 1526453107000,
      //       deptId: 17,
      //       deptName: 'v',
      //       deptNo: 'v',
      //       parentId: 1
      //     }
      //   ]
      // }
      // this.loading = false
      // this.listData = res.data
      // // 分页赋值
      // this.pageparm.currentPage = this.formInline.page
      // this.pageparm.pageSize = this.formInline.limit
      // this.pageparm.total = res.count
      // // 模拟数据结束

      /***
       * 调用接口，注释上面模拟数据 取消下面注释
       */
      deptList(parameter)
      axios.post('/api/drill/' + parameter.page + '/' + parameter.limit,
        JSON.stringify({
          drillName:this.select
        }),
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
            this.pageparm.total = res.total
            this.select=""
          }
        })
        .catch(err => {
          this.loading = false
          this.$message.error('菜单加载失败，请稍后再试！')
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
        this.editForm.deptId = row.deptId
        this.editForm.deptName = row.deptName
        this.editForm.deptNo = row.deptNo
      } else {
        this.title = '添加'
        this.editForm.deptId = ''
        this.editForm.deptName = ''
        this.editForm.deptNo = ''
      }
    },
    // 编辑、增加页面保存方法
    submitForm(editData) {
      console.log( JSON.stringify(this.editForm))
      this.$refs[editData].validate(valid => {
        if (valid) {
          this.loading = true
          axios.post('/api/drill/', JSON.stringify(this.editForm), {
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
                  message: '训练师信息保存成功！'
                })
                this.addfrom = false;
                this.loading = false;
              } else {
                this.$message({
                  type: 'info',
                  message: res.msg
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

      this.addForm = false;
    },
    get(index, row){
      if (row != undefined && row != 'undefined') {
        this.title = '修改'
        this.editForm.drillName = row.drillName
        this.editForm.drillNote = row.drillNote
        this.editForm.drillSex = row.drillSex
        this.editForm.drillStyle = row.drillStyle
        this.editForm.id=row.id;
        this.editForm.drillNumber= row.drillNumber
        this.editFormVisible=true;
      }
    },
    update(){
      console.log(JSON.stringify(this.editForm))
      axios.put('/api/drill/', JSON.stringify(this.editForm), {
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
              message: '教练信息保存成功！'
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
      this.addForm=false

    },
    // 删除公司
    deleteUser(index, row) {
      this.$confirm('确定要删除吗?', '信息', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(() => {
          axios.delete('/api/drill/'+row.id)
            .then(res => {
              if (res) {
                this.$message({
                  type: 'success',
                  message: '公司已删除!'
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
              this.$message.error('公司删除失败，请稍后再试！')
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

