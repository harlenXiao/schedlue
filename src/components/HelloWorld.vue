<template>
  <div class="hello">
      <i-form>
        <!-- <i-form-item v-for="item in formItem" :label="item.label" :label-width="80" :key="item.key">
            <i-input v-model="item.value" :placeholder="`${item.label}计划时间`"></i-input>
        </i-form-item> -->
        <i-form-item v-for="item in formItem" :label="item.label" :label-width="80" :key="item.key">
            <i-row>
                <i-col span="11">
                    <i-date-picker type="date" placeholder="Select date" v-model="item.startDate"></i-date-picker>
                </i-col>
                <i-col span="2" style="text-align: center">-</i-col>
                <i-col span="11">
                    <i-date-picker type="date" placeholder="Select time" v-model="item.endDate"></i-date-picker>
                </i-col>
            </i-row>
            <!-- <i-input v-model="item.value" :placeholder="`${item.label}计划时间`"></i-input> -->
        </i-form-item>
        <!-- <i-form-item label="开始时间">
            <i-row>
                <i-col span="50">
                    <i-date-picker type="date" placeholder="开始时间" v-model="startTime"></i-date-picker>
                </i-col>
            </i-row>
        </i-form-item> -->
      </i-form>

      <!-- <i-form class="form" ref="formDynamic" :model="holidayList" :label-width="100" style="width: 400px">
        <i-form-item 
                v-for="(item, index) in holidayList.items"
                v-if="item.status"
                :key="index"
                :label="'特殊休假 ' + item.index"
                :prop="'items.' + index + '.value'"
                :rules="{required: true, type: 'date', message: 'Item ' + item.index +' can not be empty', trigger: 'change'}">
            <i-row>
                <i-col span="15">
                    <i-date-picker type="date" placeholder="特殊休假" v-model="item.value"></i-date-picker>
                </i-col>
                <i-col span="4" offset="1">
                    <i-button @click="handleRemove(index)">Delete</i-button>
                </i-col>
            </i-row>
        </i-form-item>
        <i-form-item>
            <i-row>
                <i-col span="12">
                    <i-button type="dashed" long @click="handleAdd" icon="md-add">Add item</i-button>
                </i-col>
            </i-row>
        </i-form-item>
        <i-form-item>
            <i-button type="primary" @click="handleSubmit('formDynamic')">Submit</i-button>
            <i-button @click="handleReset('formDynamic')" style="margin-left: 8px">Reset</i-button>
        </i-form-item>
    </i-form> -->
    <i-button type="primary" @click="run">生成排期表</i-button>
    <h1>手机号收集</h1>

    <div class="table-wrap" v-if="tableDateList.length">
      <div class="table">
        <div class="thead">
          <div class="tr">
            <div class="th" :style="{width: 1/tableDateList.length*100 + '%'}" v-for="(item, index) in tableDateList" :key="index">{{item | fileterTime}}</div>
          </div>
        </div>
        <div class="tbody">
          <div class="tr" v-if="item1.startDate" :class="[item1.key]" v-for="(item1, key1) in formItem" :key="key1">
            <div class="td" :class="{active: isActive(item1, item)}" :style="{width: 1/tableDateList.length*100 + '%'}" v-for="(item, index) in tableDateList" :key="index">
            {{isActive(item1, item) ? item1.label : ''}}
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import moment from 'moment'
export default {
  name: "HelloWorld",
  data() {
    return {
      msg: "Welcome to Your Vue.js App",
      formItem: [
        {
          label: 'UI',
          key: 'ui',
          value: '',
          startDate: null,
          endDate: null
        },
        {
          label: '活动系统、网关',
          key: 'gateway',
          value: '',
          startDate: null,
          endDate: null
        },
        {
          label: '前端',
          key: 'front',
          value: '',
          startDate: null,
          endDate: null
        },
        {
          label: '测试',
          key: 'test',
          value: '',
          startDate: null,
          endDate: null
        },
        {
          label: '运营',
          key: 'operator',
          value: '',
          startDate: null,
          endDate: null
        }
      ],
      startTime: null,
      endTime: null,
      index: 1,
      holidayList: {
          items: [
            {
              value: '',
              index: 1,
              status: 1
            }
          ]
      },
      tableDateList: []

    };
  },
  methods: {
    run() {
      this.computationTime()
    },
    // 计算总时间
    computationTime() {
      let totalDate = 0 // 总天数 包含休假
      let tempTableDateList = []


      // let indexDate = this.startTime
      let tempFormItem = JSON.parse(JSON.stringify(this.formItem))
      
      for (let i in tempFormItem) {
        if (tempFormItem[i].endDate && tempFormItem[i].startDate) {
          if (!this.startTime) {
            this.startTime = tempFormItem[i].startDate
          }
        }

        if (tempFormItem[i].endDate) {
          this.endTime = tempFormItem[i].endDate
        }
        // if (tempFormItem[i].value) {
        //   totalDate = realTotalDate
        //   tempFormItem[i].startDate = indexDate
        //   tempFormItem[i].endDate = moment(indexDate).add(Number(tempFormItem[i].value) - 1, 'days')
        //   indexDate = moment(indexDate).add(Number(tempFormItem[i].value), 'days')
        // }
      }
      // this.formItem = tempFormItem

      totalDate = moment(this.endTime).diff(moment(this.startTime), 'day') + 1
      console.log(totalDate)
      for (let i=0; i < totalDate; i++) {
        let indexTime = moment(this.startTime).add(i, 'days')
        console.log(indexTime)
        tempTableDateList[i] = indexTime
      }
      this.tableDateList = tempTableDateList

      console.log(this.tableDateList)
      // console.log(this.formItem)
    },
    isActive(item, indexDate) {
      if (moment(item.startDate).isBefore(moment(indexDate)) && moment(item.endDate).isAfter(moment(indexDate)) || 
          moment(item.startDate).isSame(moment(indexDate)) || moment(item.endDate).isSame(moment(indexDate))) {
        return true
      }
      return false
    },
    handleSubmit (name) {
      this.$refs[name].validate((valid) => {
        if (valid) {
            this.$Message.success('Success!');
        } else {
            this.$Message.error('Fail!');
        }
      })
    },
    handleReset (name) {
        this.$refs[name].resetFields();
    },
    handleAdd () {
      this.index++;
      this.holidayList.items.push({
          value: '',
          index: this.index,
          status: 1
      });
    },
    handleRemove (index) {
      this.holidayList.items[index].status = 0;
    }
  },
  filters: {
    fileterTime(val) {
      return `${moment(val).month() + 1}月${moment(val).date()}日`
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
.form {
  margin:  0 auto;
}

h1 {
  margin-top: 30px;
}
.table-wrap {
  width: 90%;
  margin: 30px auto 0;
  .table {
    border-bottom: 1px solid #000;
    .tr {
      display: flex;
      .th, .td {
        border: 1px solid #000;
        border-right: none;
        border-bottom: none;

        &:last-child {
          border-right: 1px solid #000;
        }
      }

      &.ui {
        .td {

          &.active {
            background: #2d8cf0;
          }
        }
      }
      &.gateway {
        .td {
          &.active {
            background: #19be6b;
          }
        }
      }
      &.front {
        .td {
          &.active {
            background: #ff9900;
          }
        }
      }
      &.test {
        .td {
          &.active {
            background: yellow;
          }
        }
      }
      &.operator {
        .td {
          &.active {
            background: #ed4014;
          }
        }
      }
    }
  }
}


</style>
