/**
 * 系统管理 菜单管理
 */
<template>
  <div>
    <!-- 面包屑导航 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>个人管理</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 主体内容 -->
    <el-row>
      <!-- title -->
      <div class="modt-box">用户管理</div>
      <el-col :span="4"> </el-col>


        <el-form ref="form" :model="form" label-width="200px" :rules="rules">
          <el-form-item :label="peopledatakey.name" prop="moduleName">
            <el-input size="small" v-model="peopledatavalue.name"></el-input>
          </el-form-item>
          <el-form-item :label="peopledatakey.data2" prop="moduleName">
            <el-input size="small" v-model="peopledatavalue.data2"></el-input>
          </el-form-item>
          <el-form-item :label="peopledatakey.data3" prop="moduleName">
            <el-input size="small" v-model="peopledatavalue.data3"></el-input>
          </el-form-item>
          <el-form-item :label="peopledatakey.data4" prop="moduleName" :disabled=true>
            <el-input size="small" v-model="peopledatavalue.data4":disabled=true></el-input>
          </el-form-item>
          <el-form-item :label="peopledatakey.data5" prop="moduleName">
            <el-input size="small" v-model="peopledatavalue.data5":disabled=true></el-input>
          </el-form-item>
          <el-form-item>
            <el-button size="small" type="primary" @click=" submitForm('form')">保存</el-button>
            <el-button size="small" type="primary" v-show="showdelete" @click="deleteModule">删除</el-button>
          </el-form-item>
        </el-form>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import {
  ModuleList,
  ModuleGet,
  ModuleNodes,
  ModuleSave,
  ModuleDelete
} from '../../api/userMG'
import axios from "axios";
export default {
  data() {
    return {
      showdelete: false,
      treeData: [],
      defaultProps: {
        children: 'children',
        label: 'name'
      },

      peopledatakey:{
        name:"",
        data2:"",
        data3:"",
        data4:"",
        data5:"",
      },
      peopledatavalue:{
        name:"",
        data2:"",
        data3:"",
        data4:"",
        data5:"",
      },
      editForm: {
        id: '',
        studentId: '',
        studentNumber: '',
        drillTeacher: "",
        studentLevel: "",
        studentName:"",
        drillNumber:"",
      },
      editForm2: {
        id: '',
        drillNumber: '',
        drillStyle: "",
        drillNote: "",
        drillName:"",
        drillSex:"",
      },
      editForm3: {
        id: '',
        managePost: '',
        manageTag: "",
        manageName: "",
        manageIdea:"",
      },
      form: {
        addUser: '',
        editUser: '',
        addTime: '',
        editTime: '',
        moduleId: '',
        parentId: '',
        moduleLevel: '',
        systemNo: '',
        isLeaf: '',
        fullIndex: '',
        moduleIcon: '',
        moduleOrder: '',
        moduleName: '',
        moduleNotes: '',
        moduleUrl: ''
      },
      // rules表单验证
      tag:localStorage.getItem('tag'),
      fmenu: []
    }
  },
  /**
   * 创建完毕
   */

  created() {
    let tag=localStorage.getItem('tag');
    this.getdata(tag);
    console.log(this.peopledatavalue);

  },

  /**
   * 里面的方法只有被调用才会执行
   */
  methods: {
    // 获取数据
    getdata(tag) {
      let id = localStorage.getItem('id')
      if (tag == 2) {
        axios.get('/api/student/' + id,{}).then(res => {
          if (res.success == false) {
            this.$message({
              type: 'info',
              message: res.msg
            })
          } else {
            console.log(id);
            this.peopledatakey.name = "学生姓名";
            this.peopledatakey.data2 = "学生性别";
            this.peopledatakey.data3 = "学生号码";
            this.peopledatakey.data4 = "学生等级";
            this.peopledatakey.data5 = "学生教练";
            this.peopledatavalue.name = res.data.studentName;
            this.peopledatavalue.data2 = "男";
            this.peopledatavalue.data3 = res.data.studentNumber;
            this.peopledatavalue.data4 = res.data.studentLevel;
            this.peopledatavalue.data5 = res.data.drillTeacher;
            this.editForm.id=res.data.id;
            this.editForm.studentId=res.data.studentId;
            this.editForm.drillTeacher=res.data.drillTeacher;
            this.editForm.studentLevel=res.data.studentLevel;
            this.editForm.studentName=res.data.studentName;
            this.editForm.drillNumber=res.data.drillNumber;
          }
        })
      }
      if (tag == 1) {
        axios.get('/api/drill/' + id,{}).then(res => {
          if (res.success == false) {
            this.$message({
              type: 'info',
              message: res.msg
            })
          } else {
            this.peopledatakey.name = "训练师姓名";
            this.peopledatakey.data2 = "训练师性别";
            this.peopledatakey.data3 = "训练师号码";
            this.peopledatakey.data4 = "训练师风格";
            this.peopledatakey.data5 = "训练师简介";
            this.peopledatavalue.name = res.data.drillName;
            this.peopledatavalue.data2 = "男";
            this.peopledatavalue.data3 = res.data.drillNumber;
            this.peopledatavalue.data4 = res.data.drillStyle;
            this.peopledatavalue.data5 = res.data.drillNote;
            this.editForm2.id=res.data.id;
            this.editForm2.drillName=res.data.drillName;
            this.editForm2.drillSex=res.data.drillSex;
            this.editForm2.drillNumber=res.data.drillNumber;
            this.editForm2.drillStyle=res.data.drillStyle;
            this.editForm2.drillNote=res.data.drillNote;
          }
        })
      }
      if(tag==0){
        axios.get('/api/manage/' + id,{}).then(res => {
          if (res.success == false) {
            this.$message({
              type: 'info',
              message: res.msg
            })
          } else {
            this.peopledatakey.name = "管理员名称";
            this.peopledatakey.data2 = "管理员职位";
            this.peopledatakey.data3 = "管理员简介";
            this.peopledatakey.data4 = "管理员认证id";
            this.peopledatakey.data5 = "管理员权限等级";
            this.peopledatavalue.name = res.data.manageName;
            this.peopledatavalue.data2 = res.data.managePost;
            this.peopledatavalue.data3 = res.data.manageIdea;
            this.peopledatavalue.data4 = res.data.id;
            this.peopledatavalue.data5 = res.data.manageTag;
            this.editForm3.id=res.data.id;
            this.editForm3.manageName=res.data.manageName;
            this.editForm3.managePost=res.data.managePost;
            this.editForm3.manageTag=res.data.manageTag;
            this.editForm3.manageIdea=res.data.manageIdea;
            console.log(JSON.stringify(this.editForm3));
          }
        })
      }

    },

    // 添加
    addModule() {
      this.showdelete = false
      this.form.addUser = ''
      this.form.editUser = ''
      this.form.addTime = ''
      this.form.editTime = ''
      this.form.moduleId = ''
      this.form.parentId = ''
      this.form.moduleLevel = ''
      this.form.systemNo = ''
      this.form.isLeaf = ''
      this.form.fullIndex = ''
      this.form.moduleIcon = ''
      this.form.moduleOrder = ''
      this.form.moduleName = ''
      this.form.moduleNotes = ''
      this.form.moduleUrl = ''
    },
    // 获取父级菜单
    getmenu() {
      let parm = {
        page: '1',
        limit: '10'
      }
      ModuleNodes(parm)
        .then(res => {
          this.fmenu = res.data
        })
        .catch(err => {
          this.loading = false
          this.$message.error('父级菜单列表获取失败，请稍后再试！')
        })
    },
    // 复选变单选
    handleClick(data, checked, node) {
      if (checked) {
        this.$refs.tree.setCheckedNodes([])
        this.$refs.tree.setCheckedNodes([data])
        this.showdelete = true
      } else {
      }
    },
    // 点击节点
    nodeclick(arr, node, self) {
      ModuleGet(arr.id)
        .then(res => {
          console.log(JSON.stringify(res))
          this.form = res.data.data
          this.$refs.tree.setCheckedNodes([])
          this.$refs.tree.setCheckedNodes([arr])
        })
        .catch(err => {
          this.loading = false
          this.$message.error('用户管理获取失败，请稍后再试！')
        })
    },
    submitForm(editData) {
      if(this.tag==2){
        this.editForm.studentName=this.peopledatavalue.name;
        this.editForm.studentNumber=this.peopledatavalue.data3;
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
      } else if(this.tag==1){
        this.editForm2.drillName=this.peopledatavalue.name;
        this.editForm2.drillNumber=this.peopledatavalue.data3;
        this.$refs[editData].validate(valid => {
          if (valid) {
            this.loading = true
            axios.put('/api/drill/', JSON.stringify(this.editForm2), {
              headers: {
                'Content-Type': 'application/json'
              }
            })
              .then(res => {
                this.editFormVisible = false
                this.loading = false
                if (res) {
                  this.$message({
                    type: 'success',
                    message: '信息保存成功！'
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
      }else if(this.tag==0){
        this.editForm3.managePost=this.peopledatavalue.data2;
        this.editForm3.manageName=this.peopledatavalue.name;
        this.editForm3.manageIdea=this.peopledatavalue.data3;
        console.log(JSON.stringify(this.editForm3));
        this.$refs[editData].validate(valid => {
          if (valid) {
            this.loading = true
            axios.put('/api/manage/', JSON.stringify(this.editForm3), {
              headers: {
                'Content-Type': 'application/json'
              }
            })
              .then(res => {
                this.editFormVisible = false
                this.loading = false
                if (res) {
                  this.$message({
                    type: 'success',
                    message: '信息保存成功！'
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
      }
    },
    // 保存菜单
    saveModule(editData) {
      this.$refs[editData].validate(valid => {
        if (valid) {
          ModuleSave(this.form)
            .then(res => {
              this.getdata()
              this.getmenu()
            })
            .catch(err => {
              this.$message.error('菜单管理列表保存失败，请稍后再试！')
            })
        } else {
          return false
        }
      })
    },
    // 删除菜单
    deleteModule() {
      ModuleDelete(this.form.moduleId)
        .then(res => {
          this.getdata()
          this.getmenu()
          this.$message.error('菜单管理列表删除成功！')
        })
        .catch(err => {
          this.$message.error('菜单管理列表删除失败，请稍后再试！')
        })
    }
  }
}
</script>

<style>
.user-search {
  margin-top: 20px !important;
}
.user-search td {
  margin-top: 20px !important;
}
.userRole {
  width: 100%;
}
.el-table__expanded-cell {
  padding: 0px !important;
  margin: 0px !important;
}
.modt-box {
  width: 100%;
  height: 60px;
  line-height: 60px;
  background-color: #f0f2f5;
  text-align: center;
  color: #334157;
  margin-top: 20px;
  margin-bottom: 20px;
  font-size: 24px;
}
.mod-btnbox {
  margin-bottom: 20px;
}
.treeclass {
  border: 1px solid #f3f3f3;
  padding-top: 20px;
  padding-bottom: 20px;
}
.selectw {
  width: 100%;
}
</style>

