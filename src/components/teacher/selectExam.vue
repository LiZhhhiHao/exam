//查询所有考试
<template>
  <div class="exam">
    <div class="addexam">
      <el-button
        type="primary"
        icon="el-icon-plus"
        @click="add_dialogVisible = true"
        >添加试卷</el-button
      >
    </div>
    <el-table :data="pagination.records" border>
      <el-table-column
        fixed="left"
        prop="source"
        label="试卷名称"
        width="180"
      ></el-table-column>
      <el-table-column
        prop="description"
        label="介绍"
        width="200"
      ></el-table-column>
      <el-table-column
        prop="institute"
        label="所属学院"
        width="120"
      ></el-table-column>
      <el-table-column
        prop="major"
        label="所属专业"
        width="200"
      ></el-table-column>
      <el-table-column prop="grade" label="年级" width="100"></el-table-column>
      <el-table-column
        prop="examDate"
        label="考试日期"
        width="120"
      ></el-table-column>
      <el-table-column
        prop="totalTime"
        label="持续时间"
        width="120"
      ></el-table-column>
      <el-table-column
        prop="totalScore"
        label="总分"
        width="120"
      ></el-table-column>
      <el-table-column
        prop="type"
        label="试卷类型"
        width="120"
      ></el-table-column>
      <el-table-column
        prop="tips"
        label="考生提示"
        width="400"
      ></el-table-column>
      <el-table-column fixed="right" label="操作" width="150">
        <template slot-scope="scope">
          <el-button
            @click="edit(scope.row.examCode)"
            type="primary"
            size="small"
            >编辑</el-button
          >
          <el-button
            @click="deleteRecord(scope.row.examCode)"
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
      :page-sizes="[4, 8, 10, 20]"
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
          <el-form-item label="试卷名称">
            <el-input v-model="form.source"></el-input>
          </el-form-item>
          <el-form-item label="介绍">
            <el-input v-model="form.description"></el-input>
          </el-form-item>
          <el-form-item label="所属学院">
            <el-input v-model="form.institute"></el-input>
          </el-form-item>
          <el-form-item label="所属专业">
            <el-input v-model="form.major"></el-input>
          </el-form-item>
          <el-form-item label="年级">
            <el-input v-model="form.grade"></el-input>
          </el-form-item>
          <el-form-item label="考试日期">
            <el-col :span="11">
              <el-date-picker
                type="date"
                placeholder="选择日期"
                v-model="form.examDate"
                style="width: 100%"
              ></el-date-picker>
            </el-col>
          </el-form-item>
          <el-form-item label="持续时间">
            <el-input v-model="form.totalTime"></el-input>
          </el-form-item>
          <el-form-item label="总分">
            <el-input v-model="form.totalScore"></el-input>
          </el-form-item>
          <el-form-item label="试卷类型">
            <el-input v-model="form.type"></el-input>
          </el-form-item>
          <el-form-item label="考生提示">
            <el-input type="textarea" v-model="form.tips"></el-input>
          </el-form-item>
        </el-form>
      </section>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="submit()">确 定</el-button>
      </span>
    </el-dialog>

    <el-dialog title="添加试卷" :visible.sync="add_dialogVisible" width="30%">
      <!-- :before-close="handleClose" -->
      <section class="add">
        <el-form
          ref="addforms"
          :model="addform"
          label-width="80px"
          :rules="rules"
        >
          <el-form-item label="试卷名称" prop="source">
            <el-input v-model="addform.source"></el-input>
          </el-form-item>
          <el-form-item label="介绍" prop="description">
            <el-input v-model="addform.description"></el-input>
          </el-form-item>
          <el-form-item label="所属学院" prop="institute">
            <el-input v-model="addform.institute"></el-input>
          </el-form-item>
          <el-form-item label="所属专业" prop="major">
            <el-input v-model="addform.major"></el-input>
          </el-form-item>
          <el-form-item label="年级" prop="grade">
            <el-input v-model="addform.grade"></el-input>
          </el-form-item>
          <el-form-item label="考试日期" prop="examDate">
            <el-col :span="11">
              <el-date-picker
                placeholder="选择日期"
                v-model="addform.examDate"
                style="width: 100%"
              ></el-date-picker>
            </el-col>
          </el-form-item>
          <el-form-item label="持续时间" prop="totalTime">
            <el-input v-model="addform.totalTime"></el-input>
          </el-form-item>
          <el-form-item label="总分" prop="totalScore">
            <el-input v-model="addform.totalScore"></el-input>
          </el-form-item>
          <el-form-item label="考试类型" prop="type">
            <el-input v-model="addform.type"></el-input>
          </el-form-item>
          <el-form-item label="考生提示" prop="tips">
            <el-input type="textarea" v-model="addform.tips"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="onSubmit('addforms')"
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
    return {
      form: {}, //保存点击以后当前试卷的信息
      pagination: {
        //分页后的考试信息
        current: 1, //当前页
        total: null, //记录条数
        size: 4, //每页条数
      },
      addform: {
        //表单数据初始化
        source: null,
        description: null,
        institute: null,
        major: null,
        grade: null,
        examDate: null,
        totalTime: null,
        totalScore: null,
        type: null,
        tips: null,
        paperId: null,
      },
      dialogVisible: false,
      add_dialogVisible: false,
      rules: {
        source: [
          { required: true, message: "请输入试卷名称", trigger: "blur" },
          { min: 3, max: 5, message: "长度在 3 到 5 个字符", trigger: "blur" },
        ],
        description: [
          { required: true, message: "请输入介绍", trigger: "change" },
        ],
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
        grade: [
          // {
          //   required: true,
          //   message: "请输入年级",
          //   trigger: "change",
          // },
          { validator: checknum, trigger: "change", required: true },
        ],
        examDate: [
          {
            required: true,
            message: "请选择考试日期",
            trigger: "change",
          },
        ],
        totalTime: [
          // { required: true, message: "请输入持续时间", trigger: "change" },
          { validator: checknum, trigger: "change", required: true },
        ],
        totalScore: [
          // { required: true,type: "date", message: "请输入总分", trigger: "change" },
          { validator: checknum, trigger: "change", required: true },
        ],
        type: [
          { required: true, message: "请输入考试类型", trigger: "change" },
        ],
        tips: [{ required: true, message: "请输入考生提示", trigger: "blur" }],
      },
    };
  },
  created() {
    this.getExamInfo();
  },
  methods: {
    edit(examCode) {
      //编辑试卷
      this.dialogVisible = true;
      this.$axios(`/api/exam/${examCode}`).then((res) => {
        //根据试卷id请求后台
        if (res.data.code == 200) {
          this.form = res.data.data;
        }
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
    submit() {
      //提交修改后的试卷信息
      this.dialogVisible = false;
      this.$axios({
        url: "/api/exam",
        method: "put",
        data: {
          ...this.form,
        },
      }).then((res) => {
        if (res.data.code == 200) {
          this.$message({
            //成功修改提示
            message: "更新成功",
            type: "success",
          });
        }
        this.getExamInfo();
      });
    },
    deleteRecord(examCode) {
      this.$confirm("确定删除该记录吗,该操作不可逆！！！", "删除提示", {
        confirmButtonText: "确定删除",
        cancelButtonText: "算了,留着",
        type: "danger",
      })
        .then(() => {
          //确认删除
          this.$axios({
            url: `/api/exam/${examCode}`,
            method: "delete",
          }).then((res) => {
            this.getExamInfo();
          });
        })
        .catch(() => {});
    },
    getExamInfo() {
      //分页查询所有试卷信息
      this.$axios(
        `/api/exams/${this.pagination.current}/${this.pagination.size}`
      )
        .then((res) => {
          this.pagination = res.data.data;
        })
        .catch((error) => {});
    },
    //改变当前记录条数
    handleSizeChange(val) {
      this.pagination.size = val;
      this.getExamInfo();
    },
    //改变当前页码，重新发送请求
    handleCurrentChange(val) {
      this.pagination.current = val;
      this.getExamInfo();
    },
    formatTime(date) {
      //日期格式化
      console.log(date);
      let year = date.getFullYear();
      let month =
        date.getMonth() + 1 < 10
          ? "0" + (date.getMonth() + 1)
          : date.getMonth() + 1;
      let day = date.getDate() < 10 ? "0" + date.getDate() : date.getDate();
      let hours =
        date.getHours() < 10 ? "0" + date.getHours() : date.getHours();
      let minutes =
        date.getMinutes() < 10 ? "0" + date.getMinutes() : date.getMinutes();
      let seconds =
        date.getSeconds() < 10 ? "0" + date.getSeconds() : date.getSeconds();
      // 拼接
      return (
        year +
        "-" +
        month +
        "-" +
        day +
        " " +
        hours +
        ":" +
        minutes +
        ":" +
        seconds
      );
    },
    onSubmit(formName) {
      // console.log(111);'

      this.$refs[formName].validate((valid) => {
        if (valid) {
          let examDate = this.formatTime(this.addform.examDate);
          this.addform.examDate = examDate.substr(0, 10);
          this.$axios(`/api/examManagePaperId`).then((res) => {
            this.addform.paperId = res.data.data.paperId + 1; //实现paperId自增1
            this.$axios({
              url: "/api/exam",
              method: "post",
              data: {
                ...this.addform,
              },
            }).then((res) => {
              if (res.data.code == 200) {
                this.$message({
                  message: "数据添加成功",
                  type: "success",
                });
                // this.$router.push({ path: "/selectExam" });
                this.add_dialogVisible = false;
              }
            });
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
      // console.log(this.formatTime(this.addform.examDate));
    },
    cancel() {
      //取消按钮
      this.addform = {};
      this.add_dialogVisible = false;
    },
  },
};
</script>
<style lang="scss" scoped>
.exam {
  padding: 0px 40px;
  .addexam {
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
}
</style>
