<template>
  <div class="employee">
    
        <div class="tbar">
          <el-button
           icon="el-icon-refresh" 
           title="刷新" size="mini" 
           circle @click="search"
           ></el-button>

          <el-input 
          size="small" 
          @keyup.enter.native="refreshData" 
          placeholder="请输入人员名称" 
          v-model="condition" clearable
            style="width:260px;">

            <el-button 
            size="small" 
            @click="refreshData" 
            slot="append" 
            icon="el-icon-search"
            >搜索</el-button>
          </el-input>

          <el-button 
          type="primary" 
          size="small" 
          style="margin-left:10px;" 
          @click="addEmpShow"
          >新增人员
          </el-button>

          <el-button 
          type="danger" 
          size="small" :disabled="selection.length==0" >
            导出
          </el-button>
        </div>
        <div class="gridTable">
          <zj-table 
          :autoHeight='bottomDivShow' 
          ref="taskTable" 
          style="width: 100%;" 
          height='250px'
          :data="taskData" 
          tooltip-effect="dark"
          highlight-current-row row-key="emp_no"
          @row-click="handleRowClick"
          @row-dblclick="handleRowDBClick">
            
            <el-table-column 
            prop="emp_id" 
            label="序号" 
            align="center" 
            width="100"
            ></el-table-column>

            <el-table-column 
            prop="emp_no" 
            label="工号" 
            align="center" 
            width="100"
            ></el-table-column>

            <el-table-column 
            prop="emp_name" 
            label="人员名称" 
            align="center" 
            width="130"
            ></el-table-column>

            <el-table-column 
            prop="emp_sex" 
            label="性别" 
            align="center" 
            width="100">
              <template 
              slot-scope="scope">
              {{scope.row.emp_sex | empSexTrans}}
              </template>
            </el-table-column>

            <el-table-column 
            prop="emp_phone" 
            label="联系电话" 
            align="center" 
            width="150"
            ></el-table-column>

            <el-table-column 
            prop="emp_2Phone" 
            label="备用联系电话" 
            align="center" 
            width="150"
            ></el-table-column>

            <el-table-column 
            prop="emp_state" 
            label="状态" 
            align="center" 
            width="130">
            <template 
              slot-scope="scope">
              {{scope.row.emp_state | empStateTrans}}
              </template></el-table-column>
            
            <el-table-column label="操作" width="160" prop="handle">
              <template slot-scope="scope">
                <el-button type="primary" icon="el-icon-edit" size="mini" circle @click="editTaskShow(scope.row)">
                </el-button>
                <el-button type="danger" icon="el-icon-delete" size="mini" circle @click="deleteOne(scope.row)">
                </el-button>
              </template>
            </el-table-column>
            
          </zj-table>
        </div>
      <div class="bottomLayout" >
        <el-tabs 
        v-model="activeName" 
        :style="{height:bottomDivShow?'300px':'50px'}">
          <el-tab-pane label="人员技能" name="first">
            <div v-if="bottomDivShow">

              <div class="tbar">
                <el-button 
                icon="el-icon-refresh" 
                title="刷新" size="mini" 
                circle @click="searchItem"
                ></el-button>

                <el-input 
                size="small" 
                @keyup.enter.native="refreshItemData" 
                placeholder="请输入人员技能信息"
                v-model="itemCondition" 
                clearable style="width:250px;">
                  <el-button 
                  size="small" 
                  @click="refreshItemData" 
                  slot="append" 
                  icon="el-icon-search"
                  >搜索</el-button>
                </el-input>


                <el-button 
                type="primary" 
                size="small" 
                style="margin-left:10px;" 
                @click="addNewSkill"
                >新增人员技能信息
                </el-button>

              </div>
              <div class="gridTable">
                <el-table 
                ref="empItemTable" 
                v-loading="loading" 
                style="width:100%;" 
                height="200" 
                :data="empSkillData"
                tooltip-effect="dark" 
                highlight-current-row border >

                  <el-table-column 
                  type="index" 
                  label="序号"
                  width="80" 
                  align="center"
                  ></el-table-column>

                  <el-table-column 
                  prop="skill_id" 
                  label="技能名称" 
                  align="center" 
                  width="150">
                    <template 
                      slot-scope="scope">
                      {{scope.row.skill_id | renderFilter(skillDataFilter)}}
                    </template>
                  </el-table-column>

                  <el-table-column 
                  prop="sl_name" 
                  label="技能等级" 
                  align="center" 
                  width="150"
                  ></el-table-column>

                  <el-table-column 
                  prop="技能说明" 
                  label="技能说明" 
                  align="center" 
                  width="200"
                  ></el-table-column>

                  <el-table-column 
                  prop="create_date" 
                  label="评定时间" 
                  align="center" 
                  width="160"
                  ></el-table-column>

                  <el-table-column 
                  prop="se_giveperson" 
                  label="评定人" 
                  align="center"
                  width="130"
                  ></el-table-column>

                  <el-table-column 
                  label="操作" 
                  width="185" 
                  prop="handle">
                    <template slot-scope="scope">
                      <el-button 
                      type="primary" 
                      icon="el-icon-edit" 
                      size="mini" 
                      circle @click="editItemShow(scope.row)">
                      </el-button>

                      <el-button 
                      type="danger" 
                      icon="el-icon-delete" 
                      size="mini" circle
                        @click="deleteSkillOne(scope.row)">
                      </el-button>

                    </template>
                  </el-table-column>
                </el-table>
              </div>
            </div>
          </el-tab-pane>
        </el-tabs>
        <i 
        class="splitButton" 
        :class="[bottomDivShow?'el-icon-caret-bottom':'el-icon-caret-top']"
        @click="bottomDivShow=!bottomDivShow"></i>
      </div>
    

    <!-- 新增/修改人员信息 -->
    <el-dialog 
    v-if="addEmpVisiable" 
    v-dialogDrag width="450px" 
    :title="addEmpText" 
    :close-on-click-modal="false"
    :visible.sync="addEmpVisiable">

      <zj-form size="small" 
      :newDataFlag='addEmpVisiable' 
      :model="empModel" 
      label-width="100px" 
      ref="empForm"
        :rules="add_rules">

        <el-form-item label="人员编号" prop="emp_no">
          <el-input
           v-model="empModel.emp_no" 
           autocomplete="off" 
           placeholder="请填写人员编号">
          </el-input>
        </el-form-item>
        
        <el-form-item label="人员名称" prop="emp_name">
          <el-input 
          class="formItem" 
          v-model="empModel.emp_name" 
          placeholder="请填写人员名称">
          </el-input>
        </el-form-item>

        <el-form-item label="性别">
          <el-select 
          v-model="empModel.sex_type" 
          placeholder="请选择性别类型">
            <el-option 
            v-for="item in empSex_options" 
            :key="item.value" 
            :label="item.label" 
            :value="item.value">
            </el-option>
          </el-select>
        </el-form-item>

        <el-form-item 
        label="联系电话" 
        prop="emp_phone">
          <el-input 
          class="formItem" 
          v-model="empModel.emp_phone" 
          placeholder="请填写联系电话">
          </el-input>
        </el-form-item>

        <el-form-item 
        label="备用电话" 
        prop="emp_2Phone">
          <el-input 
          class="formItem" 
          v-model="empModel.emp_2Phone" 
          placeholder="请填写备用电话">
          </el-input>
        </el-form-item>

        <el-form-item label="在职状态">
          <el-select 
          v-model="empModel.state_type" 
          placeholder="请选择在职状态">
            <el-option 
            v-for="item in empState_options" 
            :key="item.value" 
            :label="item.label" 
            :value="item.value">
            </el-option>
          </el-select>
        </el-form-item>

        <el-form-item style="text-align:center;margin-right:100px;">
          <el-button 
          size="medium"
           @click="addEmpVisiable = false"
           >取&nbsp;&nbsp;消</el-button>
          <el-button 
          type="primary" 
          size="medium" 
          @click="onSaveTaskClick" style="margin-left:30px;"
          >保&nbsp;&nbsp;存
          </el-button>
        </el-form-item>
      </zj-form>
    </el-dialog>
    
    <!-- 新增人员技能信息 -->
    <el-dialog 
    v-dialogDrag width="450px" 
    :title="addSkillText" 
    :close-on-click-modal="false"
    :visible.sync="skillFormVisible">
      <zj-form size="small" 
      :newDataFlag='skillFormVisible' 
      :model="skillModel" 
      label-width="100px" 
      ref="skillForm"
        :rules="addSkill_rules">

        <el-form-item 
        label="技能名称" 
        prop="skill_id">
          <el-select 
          v-model="skillModel.skill_id" 
          ref="select_skill" 
          placeholder="请选择技能名称">
            <el-option 
            :label="skillModel.skill_name" 
            :value="skillModel.skill_id" 
            style="height:auto;padding:0;">
            <el-tree 
              :data="skillData" 
              node-key="skill_id" 
              ref="tree" 
              default-expand-all 
              :expand-on-click-node="false"
              highlight-current 
              :current-node-key="skillModel.skill_id">
                <div 
                slot-scope="{node, data}" 
                style="width:100%;user-select:none;"
                  @click="handleSelectTreeDblClick(data)">
                  {{data.skill_name}}</div>
              </el-tree>
            </el-option>
          </el-select>
        </el-form-item>

        <!-- <el-form-item 
        label="技能等级" 
        prop="sl_id">
          <el-select 
          v-model="skillModel.skill_id" 
          ref="select_skill_level" 
          placeholder="请选择技能名称">
            <el-option 
            :label="skillModel.skill_name" 
            :value="skillModel.skill_id" 
            style="height:auto;padding:0;">
            <el-tree 
              :data="skillData" 
              node-key="skill_id" 
              ref="tree" 
              default-expand-all 
              :expand-on-click-node="false"
              highlight-current 
              :current-node-key="skillModel.skill_id">
                <div 
                slot-scope="{node, data}" 
                style="width:100%;user-select:none;"
                  @click="handleSelectTreeDblClick(data)">
                  {{data.skill_name}}</div>
              </el-tree>
            </el-option>
          </el-select>
        </el-form-item>
 -->


        <el-form-item label="技能等级" prop="sl_name">
          <el-input
           v-model="skillModel.sl_name" 
           autocomplete="off" 
           placeholder="请填写技能等级">
          </el-input>
        </el-form-item>

        <el-form-item label="评定人" prop="se_giveperson">
          <el-input
           v-model="skillModel.se_giveperson" 
           autocomplete="off" 
           placeholder="请填写评定人">
          </el-input>
        </el-form-item>
  

        <!-- <el-form-item label="技能说明" prop="se_giveperson">
          <el-input 
          class="formItem" 
          type="textarea" 
          :rows="2" 
          v-model="skillModel.se_giveperson" 
          placeholder="请填写技能说明">
          </el-input>
        </el-form-item> -->

        <el-form-item style="text-align:center;margin-right:100px;">
          <el-button 
          size="medium"
           @click="skillFormVisible = false"
           >取&nbsp;&nbsp;消</el-button>
          <el-button 
          type="primary" 
          size="medium" 
          @click="onSaveSkillClick('skillForm')" 
          style="margin-left:30px;"
          >保&nbsp;&nbsp;存
          </el-button>
        </el-form-item>
      </zj-form>
    </el-dialog>
    
  </div>
</template>

<script>
export default {
  name: "employee",
  data() {
    return {
      empModel:{},
      empFormVisible: false,
      condition: "",
      addEmpText: "",
      addOrNot: true, //是否新增
      tableData:[],

      empSkillData: [], //人员技能数据
      itemCondition: "",//搜索人员技能
      skillModel:[],
      addSkillText:"",
      skillDataFilter:[],//技能名称渲染数据
      skillData:[],
      bottomDivShow: false,


      limit: 10,
      currentPage: 1,
      total: 0,

      itemListCondition: "",
      dataCondition: "",
      taskData: [], //表格数据
      skillFormVisible:false,

      
      itemListData: [], //物料
      empDataData: [], //资料
      dataTypeFilter: [],
      empDataFilter: [], //人员渲染数据
      selection: [], //选中行数据
      itemSelection: [],
      dataSelection: [],
      currentRow: {},
      addEmpVisiable: false,
      addEmpItemVisible: false,
      addEmpDataVisible: false,
      selectItemVisible: false,
      bottomDataShow: false,

      empItemModel: {},
      empItemModelList: [],
      empTechModel: {},
      addOrNot: true, //是否新增
      addTaskItemText: "",
      addTaskDataText: "",
      activeName: "first",
      loading: false,
      loading2: false,
      loading3: false,   
      empSex_options: [
        {
          value: 1,
          label: "男性"
        },
        {
          value: 0,
          label: "女性"
        }
      ],
      empState_options: [
        {
          value: 1,
          label: "在职"
        },
        {
          value: 0,
          label: "离职"
        }
      ],
      add_rules: {
        /*
        ID：emp_id
        人员编号：emp_no
        人员名称：emp_name
        电子邮箱：emp_mail
        性别：emp_sex
        联系电话：emp_phone
        备用联系电话：emp_2Phone
        出生日期：date_birth 
        在职状态（在职/离职）：emp_state
        */
        emp_no: [{ 
          required: true, message: "请填写人员编号", trigger: "blur" }
          ],
        emp_name: [{ 
          required: true, message: "请填写人员名称", trigger: "blur" }
          ],
        emp_sex: [{ 
          required: true, message: "请选择人员性别", trigger: "change" }
          ],
        emp_phone: [{ 
          required: true, message: "请填写联系电话", trigger: "blur" }
          ],
        emp_2Phone: [{ 
          required: true, message: "请填写备用电话", trigger: "blur" }
          ],
        emp_state: [{ 
          required: true, message: "请选择在职状态", trigger: "change" }
          ],

        /*
        序号：
        ID：emp_id
        技能ID：skill_id
        技能名称：skill_name
        技能等级ID:sl_id
        技能等级名称：sl_name
        技能说明：skill_note c_id
        创建时间：create_date
        评定人：create_user
        */
      },
      addSkill_rules: {

        skill_id:[{ required: true, message: "请选择技能名称", trigger: "blur" }],
        c_id:[{ required: false, message: "请选择技能等级", trigger: "blur" }],
        sl_id:[{ required: true, message: "请选择技能说明", trigger: "change" }],

        skill_name: [{ required: true, message: "请选择技能名称", trigger: "blur" }],
        sl_name: [
          { required: false, message: "请选择技能等级名称", trigger: "blur" }],
        skill_note: [
          { required: true, message: "请输入技能说明", trigger: "blur" }]
      },
    };
  },

  filters: {
    empSexTrans(value) {
      switch (value) {
        case 1:
          return "男性";
          break;
        case 0:
          return "女性";
          break;
      }
    },
    empStateTrans(value) {
      switch (value) {
        case 1:
          return "在职";
          break;
        case 0:
          return "离职";
          break;
      }
    },
    
  },

  






  watch: {
    addEmpVisiable(val) {
      if (val) {
        this.selectEmp();
      }
    }
  },
  methods: {
    //刷新人员
    refreshData() {
      this.taskData=[];
      this.bottomDivShow = false;
      this.z_get("api/employee", { condition: this.condition })
        .then(res => {
          this.taskData = res.data;
        })
        .catch(res => {});
    },
    //刷新人员技能需求
    refreshItemData() {
      this.loading = true;
      this.empSkillData = [];
      this.z_get(
        "api/skill_employee",
        {emp_id:this.currentRow.emp_id, 
        condition: this.itemCondition },
        { loading: false }
      )
        .then(res => {
          this.loading = false;
          this.skillDataFilter = res.dict.skill_id;
          this.empSkillData = res.data;
        })
        .catch(res => {});
    },
//刷新人员数据
    selectskill() {
      
      this.z_get("api/skill", { condition: "" }, { loading: false })
        .then(res => {
          //如果不一样才赋值
          if (JSON.stringify(this.deptData) != JSON.stringify(res.data)) {
            this.deptData = res.data;
          }
        })
        .catch(res => {});
    },


    refreshBottom() {
      this.itemCondition = "";
      this.empSkillData = [];
      this.dataCondition = "";
      this.taskDataData = [];
      this.bottomDivShow = false;
    },
    //新增人员
    addEmpShow() {
      this.addOrNot = true;
      this.addEmpVisiable = true;
      this.addEmpText = "";
      this.empModel = {
        emp_no : "",
        emp_name:"",
        emp_sex:1,
        emp_phone:"",
        emp_2Phone:"",
        emp_state:1,
      }

    },
    //新增技能信息
    addNewSkill(){
      this.skillFormVisible = true;
      this.addOrNot= true;
      this.addSkillText="新增人员技能信息";
      this.skillModel = {
        sl_id:"",
        skill_id:"",
        skill_name:"",
        sl_name:"",
        create_user:0,
      }
    },
    //删除单个人员技能信息
    deleteSkillOne(row) {
      this.z_delete("api/skill_employee", { data: row })
            .then(res => {
              this.$message({
                message: "删除成功",
                type: "success",
                duration: 1000
              });
              this.refreshItemData();
            })
            .catch(res => {
              this.$alert("操作失败:" + res.msg, "提示", {
                confirmButtonText: "确定",
                type: "warning"
              });
              console.log(res);
            });
    },

    //重置技能表单
    refreshForm(){
      this.$refs.skillForm.resetFields();
    },
    //新增技能编辑信息
    editSkillShow(row){
      this.skillModel = JSON.parse(JSON.stringify(row));
      
      this.skillModel.skill_name = this.renderFilter(
        this.skillModel.skill_id,
        this.skillDataFilter
      );
      this.addSkillText = "编辑人员技能信息";
      this.addOrNot = false;
      this.skillFormVisible = true;
    },
    //保存新增/编辑员工信息
    onSaveTaskClick() {
      this.$refs.empForm.validate(valid => {
        if (valid) {
          if (this.addOrNot) {
            this.z_post("api/employee", this.empModel)
              .then(res => {
                this.$message({
                  message: "新增成功!",
                  type: "success",
                  duration: 1000
                });
                this.refreshData();
                this.addEmpVisiable = false;
              })
              .catch(res => {
                this.$alert("新增失败!", "提示", {
                  confirmButtonText: "确定",
                  type: "error"
                });
              });
          } else {
            this.empModel.UpdateColumns = this.$refs.empForm.UpdateColumns;
            if (this.empModel.UpdateColumns) {
              this.z_put("api/employee", this.empModel)
                .then(res => {
                  this.$message({
                    message: "编辑成功!",
                    type: "success",
                    duration: 1000
                  });
                  this.refreshData();
                  this.addEmpVisiable = false;
                })
                .catch(res => {
                  this.$alert("编辑失败!", "提示", {
                    confirmButtonText: "确定",
                    type: "error"
                  });

                });
            } else {
              return false;
            }
          }
        } 
      });
    },
    //显示编辑人员
    editTaskShow(row) {
      this.empModel = JSON.parse(JSON.stringify(row));
      this.addEmpText = "编辑人员";
      this.addOrNot = false;
      this.addEmpVisiable = true;
    },
    //保存技能信息
    onSaveSkillClick(){
      this.$refs.skillForm.validate(valid => {
        if (valid) {
          if (this.addOrNot) {
            console.log(this.skillModel);
            this.z_post("api/skill_employee", this.skillModel)
              .then(res => {
                this.$message({
                  message: "新增成功",
                  type: "success",
                  duration: 1000
                });
                this.refreshItemData();
                this.skillFormVisible = false;
              })
              .catch(res => {

                this.$alert("新增失败", "提示", {
                  confirmButtonText: "确定",
                  type: "error"
                });
              });
          } else {
            this.skillModel.UpdateColumns = this.$refs.skillForm.UpdateColumns;
            console.log(this.skillModel)
            if (this.skillModel.UpdateColumns) {
              this.z_put("api/skill_employee", this.skillModel)
                .then(res => {
                  this.$message({
                    message: "编辑成功!",
                    type: "success",
                    duration: 1000
                  });
                  this.refreshItemData();
                  this.skillFormVisible = false;
                })
                .catch(res => {
                  this.$alert("编辑失败!", "提示", {
                    confirmButtonText: "确定",
                    type: "error"
                  });
                });
                } else {
              this.skillFormVisible = false;
            }
          }              
        } else {
          return false;
        }
      });
    }, 
    //显示编辑技能信息
    editItemShow(row){
      this.skillModel = JSON.parse(JSON.stringify(row));
      this.skillModel.skill_name = this.renderFilter(
        this.skillModel.skill_id,
        this.skillDataFilter
      );

      this.addSkillText = "编辑客户信息";
      this.addOrNot = false;
      this.skillFormVisible = true;
    },

    search() {
      this.condition = "";
      this.refreshData();
    },
    searchItem() {
      this.itemCondition = "";
      this.refreshItemData();
    },

    
    

    //刷新物料item表
    refreshItemListData() {
      this.loading2 = true;
      this.itemListData = [];
      this.z_get(
        "api/item/page",
        {
          condition: this.itemListCondition,
          page: this.currentPage,
          limit: this.limit
        },
        { loading: false }
      )
        .then(res => {
          this.loading2 = false;
          this.itemListData = res.data;
          this.total = res.total;
        })
        .catch(res => {});
    },
    refreshBottom() {
      this.itemCondition = "";
      this.dataCondition = "";
    },



    searchItemList() {
      this.itemListCondition = "";
      this.currentPage = 1;
      this.refreshItemListData();
    },
    searchData() {
      this.dataCondition = "";
    },
    mounted() {
    this.refreshData();
    this.selectDept();
  },

    

    //删除一个人员
    deleteOne(row) {
      this.z_delete("api/employee", { data: row })
            .then(res => {
              this.$message({
                message: "删除成功",
                type: "success",
                duration: 1000
              });
              this.refreshData();
            })
            .catch(res => {
              this.$alert("操作失败:" + res.msg, "提示", {
                confirmButtonText: "确定",
                type: "warning"
              });
              console.log(res);
            });
    },
    //删除一个人员
    deleteList() {
      if (this.selection.length) {
        this.onDeleteClick(this.selection);
      }
    },
    //确认删除任务
    onDeleteClick(list) {
      this.$confirm("是否删除？节点下的子节点将一并删除！", "提示", {
        confirmButtonText: "是",
        cancelButtonText: "否",
        type: "warning"
      })
        .then(() => {
          this.z_delete("api/employee/list", { data: list })
            .then(res => {
              this.$message({
                message: "删除成功!",
                type: "success",
                duration: 1000
              });
              this.refreshData();
            })
            .catch(res => {
              this.$alert("删除失败!", "提示", {
                confirmButtonText: "确定",
                type: "error"
              });
            });
        })
        .catch(() => {});
    },





    //刷新人员技能名称数据
    selectEmp() {
      this.z_get("api/skill", { condition: "" }, { loading: false })
        .then(res => {
          //如果不一样才赋值
          if (JSON.stringify(this.skillData) != JSON.stringify(res.data)) {
            this.skillData = res.data;
          }
        })
        .catch(res => {});
    },
    //双击选择人员
    handleSelectTreeDblClick(data) {
      this.skillModel.skill_id = data.skill_id;
      this.skillModel.skill_name = data.skill_name;
      this.$refs.select_skill.blur();
    },
    //点击任务行显示下面
    handleRowClick(row, column) {
      if (column.property == "handle") {
        return;
      }
      if (JSON.stringify(this.currentRow) != JSON.stringify(row)) {
        this.currentRow = row;
        //点击加载tab数据
        this.refreshBottom();
        this.refreshItemData();
      }
      this.bottomDivShow = true;
    },
    handleRowDBClick(row, column) {
      if (column.property == "handle") {
        return;
      }
      if (JSON.stringify(this.currentRow) != JSON.stringify(row)) {
        this.currentRow = row;
      }
      /* this.bottomDivShow = true; */
    },
    //删除选中的物料
    deleteSelectItem(index) {
      this.empItemModelList.splice(index, 1);
    }
  },
  //折叠所有节点
    collapseAll() {
      var icon = this.$el.getElementsByClassName("el-table__expand-icon");
      if (icon && icon.length) {
        for (var i = 0; i < icon.length; i++) {
          var classList = [];
          for (var j = 0; j < icon[i].classList.length; j++) {
            classList.push(icon[i].classList[j]);
          }
          if (classList.indexOf("el-table__expand-icon--expanded") > -1) {
            icon[i].click();
          }
        }
      }
    },
  mounted() {
    this.refreshData();
    this.selectEmp();
  }
};
</script>

<style scoped>
.standard-task {
  width: 1100px;
}
.formItem2 {
  width: 200px;
}
.gridTable {
  flex: 1;
}
.bottomLayout {
  position: relative;
}
</style>