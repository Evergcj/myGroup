<template>
  <el-container>
    <el-header class="header">
      <el-row :gutter="20">
        <el-col :span="16" :offset="4">
          我的群组
        </el-col>
      </el-row>
    </el-header>
    <el-main class="main">
      <el-row :gutter="20">
        <el-col :span="6" :offset="4">
          <el-card class="box-card">
          <el-table
            :data="tableData"
            style="width: 100%">
            <el-table-column
              fixed
              prop="groupname"
              label="组名"
              width="150">
            </el-table-column>
            <el-table-column
              fixed="right"
              label="操作"
              width="100">
              <template slot-scope="scope">
                <el-button @click="showGroupInfo(scope.$index)" type="text" size="small">查看</el-button>
                
                <el-button type="text" @click="deleteGroup(scope.$index)" size="small">删除</el-button>
              </template>
            </el-table-column>
          </el-table>
        </el-card>
        </el-col>
        <el-col :span="10">
          <el-card class="box-card">
          <el-form ref="form" :model="form" label-width="80px">
            <el-form-item label="群组名:">
              <el-input v-model="form.groupname"></el-input>
            </el-form-item>
            <el-form-item label="成员:" style="margin-bottom:7px;">
              <el-input
                type="textarea"
                :autosize="{ minRows: 2}"
                placeholder="请输入成员邮箱"
                v-model="form.member">
              </el-input>
              <!-- <el-popover
                placement="bottom"
                width="400"
                trigger="click">
                <el-tree
                  :data = "groupdetail"
                  :props="defaultProps"
                  node-key="id"
                  show-checkbox
                  @check-change="handleNodeClick"
                  ref = "membertree">
                </el-tree>
                <div slot="reference" class="memberadd el-input__inner">
                  <el-tag
                      :key = "tag.id"
                      v-for = "tag in form.member"
                      closable
                      @close="handleClose(tag)"
                      >
                    {{tag.name}}
                    </el-tag>
                </div>
                
              </el-popover> -->
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="onSubmit">确认添加</el-button>
            </el-form-item>
          </el-form>
          <el-form ref="form" :model="sendmission" label-width="80px">
            <el-form-item label="选择群组:">
              <el-select v-model="sendmission.groups" multiple filterable placeholder="请选择群组" style="width: 100%">
                <el-option
                  v-for="item in choosegroup"
                  :key="item.id"
                  :label="item.groupname"
                  :value="item.id">
                </el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="活动时间:">
              <el-date-picker type="datetime" placeholder="选择日期" value-format= "yyyy-MM-dd HH:mm:ss" v-model="sendmission.acttime" style="width: 100%;"></el-date-picker>
            </el-form-item>
            <el-form-item label="地点:">
              <el-input v-model="sendmission.location"></el-input>
            </el-form-item>
            <el-form-item label="任务:">
              <el-input
                type="textarea"
                :autosize="{ minRows: 2}"
                placeholder="请输入任务内容"
                v-model="sendmission.mission">
              </el-input>
            </el-form-item>
            <el-form-item style="margin-bottom: 0;">
              <el-button type="primary" @click="onSend">发送</el-button>
              <el-button @click= "missionClear">重置</el-button>
            </el-form-item>
          </el-form>
        </el-card>
        </el-col>
      </el-row>
    </el-main>
      <el-dialog
    title="提示"
    :visible.sync="showmsgInfo"
    width="50%"
    :modal-append-to-body="false">
    <el-form label-width="80px" :model="groupInfo">
      <el-form-item label="成员:">
        <div class="memberadd el-input__inner">
          <el-tag :key= "1110">我</el-tag>
          <el-tag
              :key = "tag.id"
              v-for = "tag in groupInfo.members"
              >
            {{tag.name}}
            </el-tag>
        </div>
      </el-form-item>
      <el-form-item label="历史:" >
        <div style="height: 200px;">
          <el-steps direction="vertical" :active = "1">
            <el-step :key="item.time"  v-for="item in groupInfo.history" :title="item.time" :description="item.description"></el-step>
          </el-steps>
        </div>
      </el-form-item>
    </el-form>
  </el-dialog>
  </el-container>

</template>

<script>
var ggroupInfo = [{id:"1",name:"多口之家",members: [{id:"1111",name:"我是你爸爸"},{id:"1112",name:"谁掐谁尿床"}]},{
        id:"2",name:"计算机交流群1401",members:[{id:"1113",name:"1401-魏无羡"},{id:"1114",name:"1401-李白"},{id:"1115",name:"1401-杜甫"}]},{
        id:"3",name:"男人帮",members:[{id:"1116",name:"李狗嗨"},{id:"1117",name:"高渐离"}]},{
        id:"4",name:"机器学习交流群",members:[{id:"1118",name:"吴恩达"}]},{
        id:"5",name:"高一家",members:[{id:"1119",name:"刘阿姨"}]}
      ];
var history = [[{time:"2018-01-01 12:12:12",description:"我是你爸爸创建群聊"},{time:"2018-01-02 12:14:01",description:"我和谁掐谁尿床加入群聊"}],
               [{time:"2014-09-13 09:01:24",description:"魏无羡创建群聊"},{time:"2014-09-14 00:01:00",description:"我和李白加入群聊"},{time:"2014-09-14 09:31:45",description:"李白邀请杜甫加入群聊"}],
               [{time:"2015-11-13 19:01:24",description:"我创建群聊"},{time:"2015-11-14 03:01:00",description:"高渐离加入群聊"},{time:"2054-11-14 03:31:45",description:"李狗嗨加入群聊"}],
               [{time:"2016-04-22 20:03:44",description:"我创建群聊"},{time:"2016-04-22 21:01:00",description:"我邀请吴恩达加入群聊"}],
               [{time:"2017-05-17 10:34:28",description:"刘阿姨创建群聊"},{time:"2017-05-17 22:18:01",description:"我加入群聊"}]];
const myMsg = {id:"1110",name:"我要上王者"};
export default {
  name: 'Index',
  data () {
    
    
    return {
      selectOptions:[],
      form: {
        groupname: '',
        member: []
      },
      sendmission: {
        groups:'',
        acttime:'',
        location:'',
        mission:''
      },
      groupInfo:{
        members:'',
        history:''
      },
      persons: {
        label: 'name',
        children: 'zones',
        isLeaf: 'leaf'
      },
      count:1,
      showmsgInfo:false,
      tableData: [],
      choosegroup: [],
      
      groupdetail: [],
      defaultProps: {
        children: 'members',
        label: 'name'
      },
      toHistory:[]
    }
  },
  methods: {
    toInit() {
      // 传回的数据结构与全局变量ggroupInfo相同
      this.$ajax.get('/groupInfo?UID=12345')
        .then(response => {
          ggroupInfo = response;
          var choosegroupData = new Array(ggroupInfo.length);
          for (var i = 0; i < ggroupInfo.length; i++) {
            choosegroupData[i] = {};
            choosegroupData[i].id = i + 1;
            choosegroupData[i].groupname = ggroupInfo[i].name;
          }
          this.choosegroup = choosegroupData;
          this.tableData = choosegroupData;
          //this.groupdetail = ggroupInfo;
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    handleClick(row) {
      console.log(row);
    },
    onSubmit() {
      // this.form = {groupname:'',member:""}
      //alert(this.form.groupname +":::"+this.form.member);
      this.$ajax.post('/createGroup', this.form)  
        .then(response => {
          var data = {};
          data.name = this.form.groupname;
          data.members = new Array();
          data.members = this.form.member;
          var len = ggroupInfo.length;
          // 返回的群id
          data.id = response.id;
          ggroupInfo.push(data);
          this.onClear();
          this.toInit();
        })  
        .catch(function(catchres) { 
          console.log(catchres);  
          console.log("连接失败") 
        })  
    },
    onClear(){
      this.form.groupname = "";
      this.form.member = [];
    },
    onSend() {
      //console.log(this.sendmission)
      this.$ajax.post('/sendmission', this.sendmission)  
        .then(response => {
          this.missionClear();
        })  
        .catch(function(catchres) { 
          console.log(catchres);  
          console.log("连接失败") 
        })  
    },
    missionClear() {
      this.sendmission.groups = [];
      this.sendmission.acttime = '';
      this.sendmission.location = '';
      this.sendmission.mission = '';
    },
    showGroupInfo(index){
      this.groupInfo.members = ggroupInfo[index].members;
      this.groupInfo.history = history[index];
      this.showmsgInfo = true;
    },
    handleNodeClick(data) {
      var data1 = this.$refs.membertree.getCheckedNodes(true);
      var nameData = new Array(data1.length)
      this.form.member = data1;
    },
    handleClose(tag) {
      this.$refs.membertree.setChecked(tag.id,false)
      this.form.member.splice(this.form.member.indexOf(tag), 1);
    },
    deleteGroup(id) {
      //{}
      this.$ajax.post('/deleteGroup', {id:id})  
        .then(response => {
          this.$refs.membertree.remove(id);
          //this.groupdetail.splice(id - 1,1);
          this.toInit();
        })  
        .catch(function(catchres) { 
          console.log(catchres);  
          console.log("连接失败") 
        })
    }
  },
  mounted() {
    this.$nextTick(function() {
      this.toInit();

    })
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.header {
  height: 66px!important;
  padding-top: 12px;
  font-size: 28px;
  font-family: "微软雅黑";
  color: #555;
}
.main {
  background: url(../assets/header.png) left top #f5f5f5;
}
.el-tag {
  margin-right: 10px;
}
.memberadd {
  width: 100%;
  cursor: pointer;
  min-height: 40px;
  height: auto!important;
}
.el-step.is-vertical .el-step__line {
    top: 14px!important;
    bottom: -12px!important;
}
</style>
