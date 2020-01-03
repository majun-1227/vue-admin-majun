<template>
    <div>
        <!-- 按钮 -->  
        员工管理<el-button type="simple" @click="toAddHandler">添加</el-button>
        <el-button type="danger">删除</el-button>
        <!-- 表格 -->
        <el-table :data="employees">
            <el-table-column label="编号" prop="id"></el-table-column>
            <el-table-column label="姓名" prop="realname"></el-table-column>
            <el-table-column label="性别" prop="gender"></el-table-column>
            <el-table-column width="120" label="手机号" prop="telephone"></el-table-column>
            <el-table-column width="200" label="身份证号" prop="idcard"></el-table-column>
            <el-table-column width="200" label="银行卡号" prop="bankkard"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <!-- 分页 -->
        <el-pagination layout="prev, pager, next" :total="50"></el-pagination>
        <!-- 模态框 -->
        <el-dialog
         title="录入员工信息"
         :visible.sync="visible"
         width="60%">
              <el-form :model="form" label-width="80px">
                <el-form-item label="用户名">
                  <el-input v-model="form.username"></el-input>
                </el-form-item>
                <el-form-item label="密码">
                  <el-input type="password" v-model="form.password"/>
                </el-form-item>
                <el-form-item label="姓名">
                  <el-input v-model="form.realname" >
                  </el-input>
                </el-form-item>
                <el-form-item label="性别" v-model="form.gender">
                  <template>
                  <el-radio-group v-model="radio">
                    <el-radio :label="3">男</el-radio>
                    <el-radio :label="6">女</el-radio>
                  </el-radio-group>
                  </template>
                  <el-input/>
                </el-form-item>
                <el-form-item label="手机号" v-model="form.telephone">
                  <el-input/>
                </el-form-item>
                <el-form-item label="身份证号" v-model="form.idcard">
                  <el-input/>
                </el-form-item>
                <el-form-item label="银行卡号" v-model="form.bankcard">
                  <el-input/>
                </el-form-item>
              </el-form>
          <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeModalHandler">取 消</el-button>
        <el-button size="small" type="primary" @click="closeModalHandler">确 定</el-button>
      </span>
    </el-dialog>
    <!-- 模态框  -->
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
  // 用于存放网页中需要调用的方法
  methods:{
    loadData(){
      let url = "http://localhost:6677/waiter/findAll"
      request.get(url).then((response)=>{
        // this->vue实例，通过vue实例访问示例中的数据
        // 将查询结果设置到customers中，this指向外部函数的this
        this.employees = response.data;
      })
    },
    submitHandler(){
      //this.form 对象 ---字符串--> 后台 {type:'customer',age:12}
      // json字符串 '{"type":"customer","age":12}'
      // request.post(url,this.form)
      // 查询字符串 type=customer&age=12
      // 通过request与后台进行交互，并且要携带参数
      let url = "http://localhost:6677//waiter/saveOrUpdate";
      request({
        url,
        method:"POST",
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },data:querystring.stringify(this.form)
      }).then((response)=>{
        // 模态框关闭
        this.closeModalHandler();
        // 刷新
        this.loadData();
        // 提示消息
        this.$message({
          type:"success",
          message:response.message
        })
      })
    },
    toDeleteHandler(id){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$message({
          type: 'success',
          message: '删除成功!'
        });
      }) 
    },
    toUpdateHandler(){
      this.visible = true;
    },
    closeModalHandler(){
      this.visible = false;
    },
    toAddHandler(){
      this.visible = true;
    }
  },
  // 用于存放要向网页中显示的数据
  data(){
     return {
      radio: 3,
      visible:false,
      employees:[],
      form:{
        type:"waiter"
      }
    }
  },
  created(){
    // this为当前vue实例对象
    // vue实例创建完毕 
    this.loadData()
  }
}
</script>

<style scoped>
 
</style>