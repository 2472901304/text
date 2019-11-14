<template>
  <el-container>
    <el-header>Header</el-header>
    <el-container>
      <el-aside width="200px">Aside</el-aside>
      <el-main>
        <el-button type="primary" @click="dialogFormVisible = true">添加</el-button>
        <el-table :data="tableData" style="width: 100%">
          <el-table-column label="姓名" prop="usename"></el-table-column>
          <el-table-column label="密码" prop="password"></el-table-column>
          <el-table-column label="身份证" prop="idcard"></el-table-column>
          <el-table-column align="right">
            <template slot="header"></template>
            <template slot-scope="scope">
              <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">
                Edit
              </el-button>
              <el-button
                size="mini"
                type="danger"
                @click="handleDelete(scope.$index, scope.row)"
              >Delete</el-button>
            </template>
          </el-table-column>
        </el-table>
        <el-pagination
          background
          layout="sizes,prev, pager, next"
          :total="total"
          :page-size="limit"
          :page-sizes="[1, 2, 3, 4]"
          @current-change="changepage"
           @size-change="handleSizeChange"
        ></el-pagination>
         <el-dialog title="收货地址" :visible.sync="dialogFormVisible">
            <el-form :model="form">
              <el-form-item label="姓名" :label-width="formLabelWidth">
                <el-input v-model="form.usename" autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="密码" :label-width="formLabelWidth">
                <el-input v-model="form.password" autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="身份证号" :label-width="formLabelWidth">
                <el-input v-model="form.idcard" autocomplete="off"></el-input>
              </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
              <el-button @click="dialogFormVisible = false">取 消</el-button>
              <el-button type="primary" @click="curFun">确 定</el-button>
            </div>
          </el-dialog>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  name: "home",
  data() {
    return {
      tableData: [], //当前页数据
      pagenum: 1, //当前页码
      limit: 3, //每页显示的条数
      total: 0,
      dialogFormVisible: false,
        form: {
          usename: '',
          password: '',
          idcard: ''
        },
        formLabelWidth: '120px',
        id:null
    };
  },
  created() {
    this.getData()
  },
  methods: {
    // 请求数据
    getData() {
      this.$http
        .get("/api/list", {
          params: { pagenum: this.pagenum, limit: this.limit }
        })
        .then(res => {
          window.console.log(res);
           if(res.data.code === 1){
              this.tableData = res.data.data;
              this.total = res.data.total;
           }else{
             alert(res.data.msg)
           }
        });
    },
    // 编辑数据
    handleEdit(index, row) {
      window.console.log(index, row);
      // 第一步 让弹窗显示
      this.dialogFormVisible = true;
      // 第二部 id 赋值  
      this.id = row.id;
      // 第三部 给弹窗的表单赋值
      this.form = {
        usename:row.usename,
        password:row.password,
        idcard:row.idcard
      }
    },
    // 删除数据
    handleDelete(index, row) {
      window.console.log(index, row);
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          // 删除接口
          this.$http.get('api/del',{params:{id:row.id}}).then(res=>{
            if(res.data.code === 1){
               this.$message({
                  type: 'success',
                  message: '删除成功!'
                });
                this.getData()
            }else{
               this.$message({
                  type: 'info',
                  message: res.data.msg
                });
            }
          })

          
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });          
        })
    },
    // 添加接口   修改
    curFun(){
         let url = ''
         if(this.id){
            url ='/api/gai'
         }else{
           url = '/api/add'
         }
         this.$http.post(url,{...this.form,id:this.id}).then(res=>{
             if(res.data.code === 1){
                this.$message({
                    type: 'success',
                    message: res.data.msg
                  });
                  this.getData();
                  this.dialogFormVisible = false;
                  this.delete();
             }else{
                this.$message({
                  type: 'info',
                  message: res.data.msg
                });
             }
         })
    },
    // 改编页码
    changepage(cur) {
      this.pagenum = cur;
      this.getData();
    },
    // 清空form
    delete(){
      this.form = {
          usename: '',
          password: '',
          idcard: ''
        },
      this.id = null
    },
    // 调整每页显示数据
    handleSizeChange(limit){
         window.console.log(limit)
         this.limit = limit;
         this.getData();
    }
    }
  }

</script>

<style scoped>
el-container {
  width: 100%;
  height: 100%;
}
.el-header,
.el-footer {
  background-color: #b3c0d1;
  color: #333;
  text-align: center;
  line-height: 60px;
}

.el-aside {
  height: 100%;
  background-color: #d3dce6;
  color: #333;
  text-align: center;
  line-height: 200px;
}

.el-main {
  height: 100%;
  background-color: #e9eef3;
}

.el-container {
  height: 100%;
  overflow: hidden;
}
</style>
