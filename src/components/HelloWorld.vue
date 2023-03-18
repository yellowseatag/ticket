<template>
  <div class="hello">
    <h1>预约剩余票据查询</h1>
    <el-row :gutter="10" type="flex" justify="center">
      <el-col :sm="24" :md="12" :lg="6">
        <el-card class="box-card">
          <div slot="header" class="clearfix">
            <h3>广州</h3>
            <span>{{GZInfo}}</span>
          </div>
          <div v-for="GZitem in GZData"  class="text item">
            <div style="font-size: 14px;color: #67C23A;">{{ GZitem["dayStr"]  + "：   剩余"+ GZitem["leftSeats"] +"个名额"}}</div>
          </div>
        </el-card>
      </el-col>
      <el-col :sm="24" :md="12" :lg="6">
        <el-card class="box-card">
          <div slot="header" class="clearfix">
            <h3>上海</h3>
            <span>{{SHInfo}}</span>
          </div>
          <div v-for="SHitem in SHData"  class="text item">
            <div style="font-size: 14px;color: #67C23A;">{{ SHitem["dayStr"]  + "   剩余票数"+ SHitem["leftSeats"] }}</div>
          </div>
        </el-card>
      </el-col>
      <el-col :sm="24" :md="12" :lg="6">
        <el-card class="box-card">
          <div slot="header" class="clearfix">
            <h3>北京</h3>
            <span>{{BJInfo}}</span>
          </div>
          <div v-for="BJitem in BJData"  class="text item el-collapse-item__content">
            <div style="font-size: 14px;color: #67C23A;">{{ BJitem["dayStr"]  + "   剩余票数"+ BJitem["leftSeats"] }}</div>
          </div>
        </el-card>
      </el-col>
      <el-col :sm="24" :md="12" :lg="6">
        <el-card class="box-card">
          <div slot="header" class="clearfix">
            <h3>厦门</h3>
            <span>{{XMInfo}}</span>
            <el-button style="float: right; padding: 3px 0" type="text"></el-button>
          </div>
          <div v-for="XMitem in XMData"  class="text item el-collapse-item__content">
            <div style="font-size: 14px;color: #67C23A;">{{ XMitem["dayStr"]  + "   剩余票数"+ XMitem["leftSeats"] }}</div>
          </div>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
  import axios from "axios";

  export default {
    name: 'HelloWorld',
    data() {
      return {
        refreshData:null,
        searchCount:0,
        GZInfo:"",
        SHInfo:"",
        BJInfo:"",
        XMInfo:"",
        GZData:[],
        SHData:[],
        BJData:[],
        XMData:[]
      }
    },
    mounted: function () {
      this.refreshData=setInterval(()=>{
        //毫秒时间戳
        let anniTime = new Date().getTime();
        //随机数
        let randomStr = Math.floor(Math.random() * (999999999 - 100000000) ) + 100000000;
        //广州
        this.getReadData(793098, 2668, anniTime, randomStr);
        //上海
        this.getReadData(790545, 2619, anniTime, randomStr);
        //北京
        this.getReadData(793610, 3063, anniTime, randomStr);
        //厦门
        this.getReadData(792057, 2635, anniTime, randomStr);
      },1000*5);
      //毫秒时间戳
      let anniTime = new Date().getTime();
      //随机数
      let randomStr = Math.floor(Math.random() * (999999999 - 100000000) ) + 100000000;
      //广州
      this.getReadData(793098, 2668, anniTime, randomStr);
      //上海
      this.getReadData(790545, 2619, anniTime, randomStr);
      //北京
      this.getReadData(793610, 3063, anniTime, randomStr);
      //厦门
      this.getReadData(792057, 2635, anniTime, randomStr);

    },
    methods: {
      getReadData: function (qnId, qtId, anniTime, randomStr) {
        const _this = this;
        axios({
          method: "post",
          headers: {'content-type': 'application/x-www-form-urlencoded'},
          url: "/api/wjc/qn/qnContent",
          data: {
            qnId: qnId,
            userId: 9496766,
            qtId: qtId,
            demoMode: false,
            anniTime: anniTime,
            randomStr: randomStr
          }
        }).then(function (response) {
          _this.searchCount++;
          let result = response.data;
          if (result.code===0){
            let qtTimes = result.data['qtTimes'];
            let newQtTimes =[];
            qtTimes.forEach(function(item,index){
            let seats = parseInt(item["seats"]);
            let subCount = parseInt(item["subCount"]);
            if (isNaN(subCount)){
              subCount = 0;
            }
            if (seats!==subCount){
              let sTime = ('0' + item["sTime"]).substr(-4);
              item["sTime"] = sTime.substr(0, 2) + ':' + sTime.substr(-2);
              let eTime = ('0' + item["eTime"]).substr(-4);
              item["eTime"] = eTime.substr(0, 2) + ':' + eTime.substr(-2);
              item["dayStr"] = item["day"] + " "+ item["sTime"] + "~" + item["eTime"];
              item["leftSeats"] = seats-subCount;
              newQtTimes.push(item);
            }
            });
            switch (result.data['id']) {
              case 793098:
                _this.GZData = newQtTimes;
                _this.GZInfo = "第"+_this.searchCount+"次查询，"+new Date().toLocaleTimeString()+ "，最新开放日期："+qtTimes[qtTimes.length-1]["day"];
                break;
              case 790545:
                _this.SHData = newQtTimes;
                _this.SHInfo = "第"+_this.searchCount+"次查询，"+new Date().toLocaleTimeString() + "，最新开放日期："+qtTimes[qtTimes.length-1]["day"];
                break;
              case 793610:
                _this.BJData = newQtTimes;
                _this.BJInfo = "第"+_this.searchCount+"次查询，"+new Date().toLocaleTimeString() + "，最新开放日期："+qtTimes[qtTimes.length-1]["day"];
                break;
              case 792057:
                _this.XMData = newQtTimes;
                _this.XMInfo = "第"+_this.searchCount+"次查询，"+new Date().toLocaleTimeString() + "，最新开放日期："+qtTimes[qtTimes.length-1]["day"];
                break;
            }
          }
        }).catch(function (error) {
          console.log(error);
        });
      }
    },
    beforeDestroy(){
      clearInterval(this.refreshData);
      this.refreshData=null;
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  h1, h2 {
    font-weight: normal;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    display: inline-block;
    margin: 0 10px;
  }

  a {
    color: #42b983;
  }
</style>
