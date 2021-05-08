// 学生管理页面
<template>
  <div class="all">
    <div class="addstu">
      <el-button
        type="primary"
        icon="el-icon-plus"
        @click="add_dialogVisible = true"
        >添加学生</el-button
      >
    </div>
    <el-table :data="pagination.records" border>
      <el-table-column
        fixed="left"
        prop="studentName"
        label="姓名"
        width="180"
      ></el-table-column>
      <el-table-column
        prop="institute"
        label="学院"
        width="200"
      ></el-table-column>
      <el-table-column prop="major" label="专业" width="200"></el-table-column>
      <el-table-column prop="grade" label="年级" width="200"></el-table-column>
      <el-table-column prop="clazz" label="班级" width="100"></el-table-column>
      <el-table-column prop="sex" label="性别" width="120"></el-table-column>
      <el-table-column
        prop="tel"
        label="联系方式"
        width="120"
      ></el-table-column>
      <el-table-column fixed="right" label="操作" width="150">
        <template slot-scope="scope">
          <el-button
            @click="checkGrade(scope.row.studentId)"
            type="primary"
            size="small"
            >编辑</el-button
          >
          <el-button
            @click="deleteById(scope.row.studentId)"
            type="danger"
            size="small"
            >删除</el-button
          >
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="pagination.current"
      :page-sizes="[6, 10]"
      :page-size="pagination.size"
      layout="total, sizes, prev, pager, next, jumper"
      :total="pagination.total"
      class="page"
    >
    </el-pagination>
    <!-- 编辑对话框-->
    <el-dialog
      title="编辑试卷信息"
      :visible.sync="dialogVisible"
      width="30%"
      :before-close="handleClose"
    >
      <section class="update">
        <el-form ref="form" :model="form" label-width="80px">
          <el-form-item label="姓名">
            <el-input v-model="form.studentName"></el-input>
          </el-form-item>
          <el-form-item label="学院">
            <el-input v-model="form.institute"></el-input>
          </el-form-item>
          <el-form-item label="专业">
            <el-input v-model="form.major"></el-input>
          </el-form-item>
          <el-form-item label="年级">
            <el-input v-model="form.grade"></el-input>
          </el-form-item>
          <el-form-item label="班级">
            <el-input v-model="form.clazz"></el-input>
          </el-form-item>
          <el-form-item label="性别">
            <el-input v-model="form.sex"></el-input>
          </el-form-item>
          <el-form-item label="电话号码">
            <el-input v-model="form.tel"></el-input>
          </el-form-item>
        </el-form>
      </section>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="submit()">确 定</el-button>
      </span>
    </el-dialog>
    <el-dialog title="添加学生" :visible.sync="add_dialogVisible" width="30%">
      <!-- :before-close="handleClose" -->
      <section class="addstu">
        <el-form
          ref="stuform"
          :model="stuform"
          label-width="80px"
          :rules="rules"
        >
          <el-form-item label="姓名" prop="studentName">
            <el-input v-model="stuform.studentName"></el-input>
          </el-form-item>
          <el-form-item label="性别" prop="sex">
            <el-input v-model="stuform.sex"></el-input>
          </el-form-item>
          <el-form-item label="学院" prop="institute">
            <el-input v-model="stuform.institute"></el-input>
          </el-form-item>
          <el-form-item label="所属专业" prop="major">
            <el-input v-model="stuform.major"></el-input>
          </el-form-item>
          <el-form-item label="年级" prop="grade">
            <el-input v-model="stuform.grade"></el-input>
          </el-form-item>
          <el-form-item label="班级" prop="clazz">
            <el-input v-model="stuform.clazz"></el-input>
          </el-form-item>
          <el-form-item label="电话号码" prop="tel">
            <el-input v-model="stuform.tel"></el-input>
          </el-form-item>
          <el-form-item label="身份证号" prop="cardId">
            <el-input v-model="stuform.cardId"></el-input>
          </el-form-item>
          <el-form-item label="邮箱" prop="email">
            <el-input v-model="stuform.email"></el-input>
          </el-form-item>
          <el-form-item label="密码" prop="pwd">
            <el-input v-model="stuform.pwd"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="onSubmit('stuform')"
              >立即创建</el-button
            >
            <el-button type="text" @click="cancel()">取消</el-button>
          </el-form-item>
        </el-form>
      </section>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    var checknum = (rule, value, callback) => {
      if (!value) {
        return callback(new Error("请输入值"));
      } else if (!Number.isInteger(Number(value))) {
        return callback(new Error("请输入数字"));
      } else {
        callback();
      }
    };
  
    var sexv = (rule, value, callback) => {
      if (!value) {
        return callback(new Error("请输入值"));
      } else if (value == "男" ? false : value == "女" ? false : true) {
        console.log(value);
        return callback(new Error("请输入正确性别"));
      } else {
        callback();
      }
    };
    var phonecheck = (rule, value, callback) => {
      let val = Number(value);
      if (!val) {
        return callback(new Error("请输入值"));
      } else if (!/^1[34578][0-9]{9}$/.test(val)) {
        // console.log(Number(value));
        return callback(
          new Error("请输入正确电话号码")
        );
      } else {
        callback();
      }
    };
  
    var cardidv = (rule, value, callback) => {
      if (!value) {
        return callback(new Error("请输入值"));
      } else if (
        !(/^[1-9][0-9]{5}(19|20)[0-9]{2}((01|03|05|07|08|10|12)(0[1-9]|[1-2][0-9]|3[0-1])|(04|06|09|11)(0[1-9]|[1-2][0-9]|30)|02(0[1-9]|[1-2][0-9]))[0-9]{3}([0-9]|x|X)$/.test(value))
      ) {
        return callback(
          new Error("请输入正确身份证号")
        );
      } else {
        callback();
      }
    };
      var emailcheck = (rule, value, callback) => {
      if (!value) {
        return callback(new Error("请输入值"));
      } else if (!/^[a-z0-9]+([._\\-]*[a-z0-9])*@([a-z0-9]+[-a-z0-9]*[a-z0-9]+.){1,63}[a-z0-9]+$/.test(value)) {
        // console.log(Number(value));
        return callback(
          new Error("请输入正确邮箱")
        );
      } else {
        callback();
      }
    };
      var passwordv = (rule, value, callback) => {
      if (!value) {
        return callback(new Error("请输入值"));
      } else if (
        !/^(?![0-9]+$)(?![a-z]+$)(?![A-Z]+$)(?!([^(0-9a-zA-Z)])+$).{6,20}$/.test(
          value
        )
      ) {
        return callback(
          new Error("请输入包含 数字,英文,字符中的两种以上，长度6-20的密码")
        );
      } else {
        callback();
      }
    };
    return {
      pagination: {
        //分页后的考试信息
        current: 1, //当前页
        total: null, //记录条数
        size: 6, //每页条数
      },
      dialogVisible: false, //对话框
      form: {}, //保存点击以后当前试卷的信息
      stuform: {
        //表单数据初始化
        studentName: null,
        grade: null,
        major: null,
        clazz: null,
        institute: null,
        tel: null,
        email: null,
        pwd: null,
        cardId: null,
        sex: null,
        role: 2,
      },
      add_dialogVisible: false,
      rules: {
        studentName: [
          { required: true, message: "请输入姓名", trigger: "blur" },
          { min: 2, max: 5, message: "长度在 3 到 5 个字符", trigger: "blur" },
        ],
        sex: [{ required: true, validator: sexv, trigger: "change" }],
        institute: [
          {
            required: true,
            message: "请输入所属学院",
            trigger: "change",
          },
        ],
        major: [
          {
            required: true,
            message: "请输入所属专业",
            trigger: "change",
          },
        ],
        grade: [{ validator: checknum, trigger: "change", required: true }],
        clazz: [
          // { required: true, message: "请输入持续时间", trigger: "change" },
          { validator: checknum, trigger: "change", required: true },
        ],
        tel: [
          // { required: true,type: "date", message: "请输入总分", trigger: "change" },
          { validator: phonecheck, trigger: "change", required: true },
        ],
        cardId: [{ validator: cardidv, trigger: "change", required: true }],
        email: [{ validator: emailcheck, trigger: "change", required: true }],
        pwd: [{ validator: passwordv, trigger: "change", required: true }],
      },
    };
  },
  created() {
    this.getStudentInfo();
  },
  methods: {
    getStudentInfo() {
      //分页查询所有试卷信息
      this.$axios(
        `/api/students/${this.pagination.current}/${this.pagination.size}`
      )
        .then((res) => {
          this.pagination = res.data.data;
        })
        .catch((error) => {});
    },
    //改变当前记录条数
    handleSizeChange(val) {
      this.pagination.size = val;
      this.getStudentInfo();
    },
    //改变当前页码，重新发送请求
    handleCurrentChange(val) {
      this.pagination.current = val;
      this.getStudentInfo();
    },
    checkGrade(studentId) {
      //修改学生信息
      this.dialogVisible = true;
      this.$axios(`/api/student/${studentId}`).then((res) => {
        this.form = res.data.data;
      });
    },
    deleteById(studentId) {
      //删除当前学生
      this.$confirm("确定删除当前学生吗？删除后无法恢复", "Warning", {
        confirmButtonText: "确定删除",
        cancelButtonText: "算了,留着吧",
        type: "danger",
      })
        .then(() => {
          //确认删除
          this.$axios({
            url: `/api/student/${studentId}`,
            method: "delete",
          }).then((res) => {
            this.getStudentInfo();
          });
        })
        .catch(() => {});
    },
    submit() {
      //提交更改
      this.dialogVisible = false;
      this.$axios({
        url: "/api/student",
        method: "put",
        data: {
          ...this.form,
        },
      }).then((res) => {
        console.log(res);
        if (res.data.code == 200) {
          this.$message({
            message: "更新成功",
            type: "success",
          });
        }
        this.getStudentInfo();
      });
    },
    handleClose(done) {
      //关闭提醒
      this.$confirm("确认关闭？")
        .then((_) => {
          done();
        })
        .catch((_) => {});
    },
    onSubmit(formName) {
      //数据提交
      this.$refs[formName].validate((valid) => {
        if (valid) {
          this.$axios({
            url: "/api/student",
            method: "post",
            data: {
              ...this.stuform,
            },
          }).then((res) => {
            if (res.data.code == 200) {
              this.$message({
                message: "数据添加成功",
                type: "success",
              });
              // this.$router.push({ path: "/studentManage" });
              this.add_dialogVisible =false;
            }
          });
          // alert("submit!");
        } else {
          console.log("error submit!!");
          this.$message({
            message: "请输入正确格式",
            type: "error",
          });
          // this.$router.push({ path: "/selectExam" });
          return false;
        }
      });
    },
    cancel() {
      //取消按钮
      this.stuform = {};
      this.add_dialogVisible = false;
    },
  },
};
</script>
<style lang="scss" scoped>
.all {
  padding: 0px 40px;
  .addstu {
    margin-bottom: 20px;
  }
  .page {
    margin-top: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .edit {
    margin-left: 20px;
  }
  .el-table tr {
    background-color: #dd5862 !important;
  }
}
.el-table .warning-row {
  background: #000 !important;
}

.el-table .success-row {
  background: #dd5862;
}
</style>
