//显示学生成绩
<template>
  <div class="table">
    <p class="title">我的分数</p>
    <section class="content-el">
      <el-table ref="filterTable" :data="score" v-loading="loading">
        <el-table-column
          prop="answerDate"
          label="考试日期"
          sortable
          width="300"
          column-key="answerDate"
          :filters="filter"
          :filter-method="filterHandler"
        >
        </el-table-column>
        <el-table-column
          prop="subject"
          label="课程名称"
          width="300"
          filter-placement="bottom-end"
        >
          <template slot-scope="scope">
            <el-tag>{{ scope.row.subject }}</el-tag>
          </template>
        </el-table-column>
        <el-table-column
          prop="etScore"
          label="考试分数"
          width="200"
        ></el-table-column>
        <el-table-column label="是否及格" width="100">
          <template slot-scope="scope">
            <el-tag :type="scope.row.etScore >= 60 ? 'success' : 'danger'">{{
              scope.row.etScore >= 60 ? "及格" : "不及格"
            }}</el-tag>
          </template>
        </el-table-column>
        <el-table-column label="考试详情" width="120">
          <template slot-scope="scope">
            <el-button type="primary" @click="getdata(scope.row)"
              >查看详情</el-button
            >
          </template>
        </el-table-column>
      </el-table>
      <el-row type="flex" justify="center" align="middle" class="pagination">
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="pagination.current"
          :page-sizes="[4, 6, 8, 10]"
          :page-size="pagination.size"
          layout="total, sizes, prev, pager, next, jumper"
          :total="pagination.total"
        >
        </el-pagination>
      </el-row>
    </section>
    <el-dialog
      title="考试详情"
      :visible.sync="examdetail"
      width="50%"
      center
      append-to-body
      lock-scroll
    >
      <el-tabs v-model="activeName">
        <el-tab-pane label="考试具体情况" name="first">
          <el-card class="box-card">
            <div slot="header" class="clearfix">
              <span>考试具体情况</span>
            </div>
            <el-form>
              <el-form-item label="考试学生">
                {{ this.$cookies.get("cname") }}
              </el-form-item>
              <el-form-item label="考试试卷">
                {{ showscore.subject }}
              </el-form-item>
              <el-form-item label="考试成绩">
                {{ showscore.etScore }}
              </el-form-item>
              <el-form-item label="考试日期">
                {{ showscore.answerDate }}
              </el-form-item>
            </el-form>
          </el-card>
        </el-tab-pane>
        <el-tab-pane label="选择题" name="second">
          <el-card class="box-card">
            <div slot="header" class="clearfix selectes">
              <span>选择题</span>
            </div>
            <div v-for="(s, idx) in select" :key="idx" class="text">
              <div class="exam_question">{{ idx + 1 }}、{{ s.question }}</div>
              <ul>
                <li>A、 {{ s.answerA }}</li>
                <li>B、 {{ s.answerB }}</li>
                <li>C、 {{ s.answerC }}</li>
                <li>D、 {{ s.answerD }}</li>
              </ul>
              <div class="des">
                <span
                  ><el-tag>总分：{{ s.score }}</el-tag></span
                >
                <span
                  ><el-tag type="success"
                    >正确答案：{{ s.rightAnswer }}</el-tag
                  ></span
                >
                <el-tag type="info">难度：{{ changelevel(s.level) }}</el-tag>
                <el-tag type="warning">专业类别:{{ s.subject }}</el-tag>
              </div>
            </div>
          </el-card>
        </el-tab-pane>
        <el-tab-pane label="填空题" name="third">
          <el-card class="box-card">
            <div slot="header" class="clearfix">
              <span>填空题</span>
            </div>
            <div v-for="(f, idx) in fillin" :key="idx" class="text item">
              <div class="exam_question">{{ idx + 1 }}、{{ f.question }}</div>
              <div class="qanswer">
                正确答案：<el-tag type="success">{{ f.answer }}</el-tag>
              </div>
              <div class="des">
                <span
                  ><el-tag>总分：{{ f.score }}</el-tag></span
                >

                <el-tag type="info">难度：{{ changelevel(f.level) }}</el-tag>
                <el-tag type="warning">专业类别:{{ f.subject }}</el-tag>
              </div>
            </div>
          </el-card>
        </el-tab-pane>
        <el-tab-pane label="判断题" name="fourth">
          <el-card class="box-card">
            <div slot="header" class="clearfix">
              <span>判断题</span>
              
            </div>
            <div v-for="(j, idx) in juedge" :key="idx" class="text item">
                <div class="exam_question">{{ idx + 1 }}、{{ j.question }}</div>
              <div class="qanswer">
                正确答案：<el-tag type="success">{{ changejudge(j.answer) }}</el-tag>
              </div>
              <div class="des">
                <span
                  ><el-tag>总分：{{ j.score }}</el-tag></span
                >

                <el-tag type="info">难度：{{ changelevel(j.level) }}</el-tag>
                <el-tag type="warning">专业类别:{{ j.subject }}</el-tag>
              </div>
            </div>
            
          </el-card>
        </el-tab-pane>
      </el-tabs>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      pagination: {
        //分页后的留言列表
        current: 1, //当前页
        total: null, //记录条数
        size: 10, //每页条数
      },
      loading: false, //加载标识符
      score: [], //学生成绩
      filter: null, //过滤参数
      examdetail: false, //考试详情展示
      activeName: "first", //标签页默认显示
      select: [],
      fillin: [],
      juedge: [],
      showscore: [],
    };
  },
  created() {
    this.getScore();
    this.loading = true; //数据加载则遮罩表格
  },
  methods: {
    getScore() {
      let studentId = this.$cookies.get("cid");
      this.$axios(
        `/api/score/${this.pagination.current}/${this.pagination.size}/${studentId}`
      ).then((res) => {
        // console.log(res);
        if (res.data.code == 200) {
          this.loading = false; //数据加载完成去掉遮罩
          this.score = res.data.data.records;
          this.pagination = { ...res.data.data };
          let mapVal = this.score.map((element, index) => {
            //通过map得到 filter:[{text,value}]形式的数组对象
            let newVal = {};
            newVal.text = element.answerDate;
            newVal.value = element.answerDate;
            return newVal;
          });
          let hash = [];
          const newArr = mapVal.reduce((item, next) => {
            //对新对象进行去重操作
            hash[next.text] ? "" : (hash[next.text] = true && item.push(next));
            return item;
          }, []);
          this.filter = newArr;
        }
      });
    },
    //改变当前记录条数
    handleSizeChange(val) {
      this.pagination.size = val;
      this.getScore();
    },
    //改变当前页码，重新发送请求
    handleCurrentChange(val) {
      this.pagination.current = val;
      this.getScore();
    },
    formatter(row, column) {
      return row.address;
    },
    filterTag(value, row) {
      return row.tag === value;
    },
    filterHandler(value, row, column) {
      const property = column["property"];
      return row[property] === value;
    },
    getdata(row) {
      console.log(row.scoreId);
      this.examdetail = true;
      this.$axios.get(`/api/examscore/${row.scoreId}`).then((data) => {
        if (data.status == 200) {
          this.select = data.data.select;
          this.fillin = data.data.fillin;
          this.juedge = data.data.juedge;
          console.log(this.juedge);
          this.showscore = data.data.score;
        } else {
          this.$message({
            message: "获取试卷信息失败",
            type: "error",
          });
        }
      });
    },
    changelevel(val) {
      if (val == 1) {
        return "简单";
      } else if (val == 2) {
        return "中等";
      } else {
        return "困难";
      }
    },
    changejudge(val){
      if (val =="T") {
        return "正确"
      }else{
        return "错误"
      }
    }
  },
};
</script>

<style lang="scss" scoped>
.pagination {
  padding-top: 20px;
}
.table {
  width: 1100px;
  margin: 0 auto;
  .title {
    margin: 20px;
  }
  .content-el {
    background-color: #fff;
    padding: 20px;
  }
}
.text {
  margin-bottom: 20px;
  ul {
    padding-left: 20px;
    margin: 30px 0;
    li {
      margin: 10px 0;
    }
  }
  .des {
    span {
      margin-right: 30px;
    }
  }
  .qanswer {
    margin: 10px 0;
  }
}
</style>
