<template>
  <div class="page-picker" style="margin-top:45px;">
    <div class="page-picker-wrapper">
      <mt-picker :columns="column1" showToolbar @confirm="onConfirm1"/>
    </div>

    <div style="padding:10px" @click="changeData">点我切换数据</div>
    <div class="page-picker-wrapper">
      <mt-picker
        :columns="column2"
        @cancel="onCancel"
        @confirm="onConfirm" 
        :title="title2" showToolbar/>
    </div>

    <div class="page-picker-wrapper">
      <mt-picker :columns="column3" :title="title3" showToolbar @confirm="onConfirm" @cancel="onCancel" :isInterrelated="true"/>
    </div>
    
    <!-- defaultIndex 参数变化 -->
    <div class="page-picker-wrapper">
      <mt-picker :columns="columns" @change="onChange4" :title="title4" showToolbar @confirm="onConfirm" @cancel="onCancel"/>
    </div>
  </div>
</template>

<style>
  @component-namespace page {
    @component picker {
      padding: 0 0 20px;
      @descendent wrapper {
        text-align: center;
        margin-bottom: 24px;
      }

      @descendent desc {
        margin: 10px 0 50px;
      }

      .mint-button {
        margin-top: 15px;
      }
    }
  }
</style>

<script type="text/babel">
import { Toast } from 'src/index';
import axios from 'axios'
export default {
  data() {
    return {
      year: new Date(),
      area: '标题',
      title2: '禁用选项并展示顶部栏',
      title3: '多列联动1',
      title4: '多列联动2',
      column1: ['杭州', '宁波', '温州', '嘉兴', '湖州'],
      column2: [
        { text: '杭州', disabled: true },
        { text: '宁波' },
        { text: '温州' }
      ],
      column3:[{
        values:[
          {text:'浙江',disabled:false,id:101,pid:-1}, 
          {text:'福建',disabled:false,id:201,pid:-1},
          {text:'江苏',disabled:false,id:301,pid:-1}],
        className:'className1',
        defaultIndex: 2
      },{
        values:[
          {text:'杭州',disabled:false,pid:101,id:10101}, 
          {text:'宁波',disabled:true,pid:101,id:10102},
          {text:'温州',disabled:false,pid:101,id:10103},
          {text:'嘉兴',disabled:false,pid:101,id:10104},
          {text:'湖州',disabled:false,pid:101,id:10105},
          {text:'福州',disabled:false,pid:201,id:20101}, 
          {text:'厦门',disabled:false,pid:201,id:20102},
          {text:'莆田',disabled:false,pid:201,id:20103},
          {text:'三明',disabled:false,pid:201,id:20104},
          {text:'泉州',disabled:false,pid:201,id:20105},
          {text:'南京',disabled:false,pid:301,id:30101}, 
          {text:'扬州',disabled:false,pid:301,id:30102},
          {text:'无锡',disabled:false,pid:301,id:30103},
          {text:'常州',disabled:false,pid:301,id:30104},
          {text:'苏州',disabled:false,pid:301,id:30105}],
        className:'className2',
        defaultIndex: 2
      }],
      column4: {
        浙江: ['杭州', '宁波', '温州', '嘉兴', '湖州'],
        福建: ['福州', '厦门', '莆田', '三明', '泉州'],
        江苏: ['南京', '扬州', '无锡', '常州', '苏州']
      },
      toastContent: (value, index) => `当前值：${value}, 当前索引：${index}`
    }
  },
  computed: {
    columns() {
      const column = this.column4;
      return [
        {
          values: Object.keys(column),
          className: 'column1'
        },
        {
          values: column[Object.keys(column)[0]],
          className: 'column2',
          defaultIndex: 2
        }
      ];
    }
  },
  methods: {
    onConfirm1(value, index ,picker) {
      Toast(`当前值：${value}, 当前索引：${index}`);
    },
    onChange4(picker, values,column,line) {
      console.log(values, column,line);
      picker.setColumnValues(1, this.column4[values[0]]);
    },
    onConfirm(value,index) {
      console.log(value,index);
    },
    onCancel(value) {
      Toast('取消');
    },
    changeData(){
      axios.get('./mock/picker.json').then(resp => {
        let respData = resp.data
        if (respData.code == '0') {
          this.column1=respData.data;
        }
      })
    }
  }
};
</script>