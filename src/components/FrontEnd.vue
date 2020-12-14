<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <div>
      
      <br>
      <table>
        
        <tr>
          <th>Select line:</th>
          <th>
            <select v-model="Lselected">
              <option v-for="line in lineList" v-bind:key="line.filter_type" v-on:click="getLineFilters">{{line.filter_type}} </option>
            </select>
          </th>
          <th>{{Lselected}}</th>
        </tr>
        <tr>
          <th>Select spesific filter</th>
          <th>
            <select v-model="Fselected">
              <option v-for="filter in filtersList" v-bind:key="filter.filter_name" v-on:click="getDatesMinMax();getFilterSensorLimits();"> {{filter.filter_name}} </option>
            </select>
          </th>
          <th>{{Fselected}}</th>
        </tr>
        <br>
        <tr>
          <th>Select days:</th>
          <th>Start date</th>
          <th>End date</th>
        </tr>
        <tr>
          <th></th>
          <th><data-picker v-model="startDate" :default-value="minDate"></data-picker></th>
          <th><data-picker v-model="endDate" :default-value="maxDate" v-on:click="getDataMinMaxMed();"></data-picker></th>
        </tr>
        <br>
      </table>
    </div>
    <br>
    <button v-on:click="getDataMinMaxMed(); getFilterDataBetweenDates();getDataBadLowerd();getDataBadUpper();">Generate</button>
    <div>
      <table border="1px">
        <tr>
          <th>Line name</th>
          <th>Filter sensor name</th>
          <th>Upper limit</th>
          <th>Lower limit</th>
          <th>Waste less </th>
          <th> Waste greater </th>
          <th>Waste SUM </th>
          <th>OK </th>
          <th>SUM All</th>
          <th>Min</th>
          <th>Max</th>
          <th>Med</th>
        </tr>
        <tr>
          <th>{{Lselected}}</th>
          <th>{{Fselected}}</th>
          <th>{{upperLimit}}</th>
          <th>{{lessLimit}}</th>
          <th>{{countLesslimit}}</th>
          <th></th>
          <th></th>
          <th></th>
          <th>{{maxData-minData}}</th>
          <th>{{minData}}</th>
          <th>{{maxData}}</th>
          <th>{{medData}}</th>
        </tr>
      </table>
    </div>
    <div>
      <LineChart :label="dateList" :chartData="dataList"></LineChart>
    </div>
    <div>
      <ScatterChart :chartdata="storage"></ScatterChart>
    </div>
  </div>
</template>

<script>

import DataPicker from 'vue2-datepicker';
import 'vue2-datepicker/index.css';
import moment from 'moment';
import axios from 'axios';
//import LineChart from './utils/LineChart'
import LineChart from './utils/LineChart.vue';
import ScatterChart from './utils/ScatterChart.vue';
//import { Line } from 'vue-chartjs';

export default {
  name: 'FrontEnd',
  components: {
    DataPicker,
    LineChart,
    ScatterChart,
  },
  data() {
    return {
      upperLimit: '',
      lessLimit: '',
      countLesslimit: '',
      countGreatlimit: '',
      startDate: '',
      endDate: '',
      Lselected: '',
      Fselected: '',
      lineList: [],
      filtersList: [],
      limitsList: [],
      filterDataList: [],
      gLable:[],
      pLable:['a', 'b', 'c'],
      dateList:[],
      dataList:[],
      minmax: [],
      minDate: '',
      maxDate: '',
      minData: '',
      maxData: '',
      medData: '',
      badLower: '',
      badUpper: '',
      good: '',
      sumData: '1551',
      storage: [],
    }
  },
  props: {
    msg: String
  },
  methods: {

     getLineList() {
      const path = "https://smartsystemsroject.herokuapp.com/api/filterTypes";
      axios.get(path)
        .then(response => {
          this.lineList = response.data.data;
          console.log(this.lineList);
        })
        .catch((error) => {
          console.error(error);
        });
    },

     getLineFilters() {
       console.log(this.Lselected);
      if(this.Lselected != ''){
        let path = "https://smartsystemsroject.herokuapp.com/api/"+this.Lselected+"/filters";
        axios.get(path)
          .then(response => {
            this.filtersList = response.data.data.reverse();
            //this.gLable = response.data.data.filter_name;
            console.log(path);
            console.log(this.Lselected);
            console.log(this.filtersList);
            for(var i = 0; i<this.filtersList.length; i++){
              console.log(this.filtersList[i].filter_name);
              this.gLable.push(this.filtersList[i].filter_name);
            }
            console.log(this.gLable);
            console.log(this.pLable);
          })
          .catch((error) => {
            console.error(error);
          });
      }
    },

    getFilterSensorLimits() {
      console.log(this.Fselected);
      if(this.Fselected != ''){
        let path = "http://localhost:2000/api/"+this.Lselected+"/"+this.Fselected+"";
        axios.get(path)
          .then(response => {
            this.limitsList = response.data.data;
            this.lessLimit = this.limitsList[0].graph_lower_limit;
            this.upperLimit = this.limitsList[0].graph_upper_limit;
            console.log(this.lessLimit);
            console.log(this.upperLimit);
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },

    getFilterDataBetweenDates() {
      this.dateList= [];
      this.dataList = [];
      if(this.startDate != '' || this.endDate != ''){
        this.startDate = moment(this.startDate).format('YYYY-MM-DD');
        this.endDate = moment(this.endDate).format('YYYY-MM-DD');
        let path= "http://localhost:2000/api/"+this.Lselected+"/"+this.Fselected+"/"+this.startDate+"&"+this.endDate;
        axios.get(path)
          .then(response => {
            this.filterDataList = response.data.data;
            for(var i=0; i<this.filterDataList.length; i++){
              this.dataList.push(this.filterDataList[i].a_data);
              this.dateList.push(this.filterDataList[i].a_date);
              var entrey = {x: this.filterDataList[i].a_date, y:this.filterDataList[i].a_data};
              this.storage.push(entrey);
            }
            console.log(this.filterDataList);
            console.log(this.dateList);
            console.log(this.dataList);
            console.log(this.storage);
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },

    getDatesMinMax() {
      if(this.Lselected!= '' && this.Fselected != ''){
        console.log("kontroll");
        let path = "http://localhost:2000/api/"+this.Lselected+"/"+this.Fselected+"/minmax";
        axios.get(path)
          .then(response => {
            this.minDate = response.data.data[0].min;
            this.maxDate = response.data.data[0].max;
            console.log(this.minDate);
            console.log(this.maxDate);
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },

    getDataMinMaxMed() {
      this.dateList= [];
      this.dataList = [];
      if(this.Lselected!= '' && this.Fselected != ''){
        console.log("kontroll");
        this.startDate = moment(this.startDate).format('YYYY-MM-DD');
        this.endDate = moment(this.endDate).format('YYYY-MM-DD');
        let path = "http://localhost:2000/api/"+this.Lselected+"/"+this.Fselected+"/"+this.startDate+"&"+this.endDate+"/minmaxmed";
        axios.get(path)
          .then(response => {
            this.minData = response.data.data[0].min;
            this.maxData = response.data.data[0].max;
            this.medData = response.data.data[0].med;
            console.log(this.minData);
            console.log(this.maxData);
            console.log(this.medData);
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },

    getDataBadLowerd() {
      this.dateList= [];
      this.dataList = [];
      if(this.Lselected!= '' && this.Fselected != '' ){
        console.log(this.lessLimit);
        this.startDate = moment(this.startDate).format('YYYY-MM-DD');
        this.endDate = moment(this.endDate).format('YYYY-MM-DD');
        let path = "http://localhost:2000/api/"+this.Lselected+"/"+this.Fselected+"/"+this.startDate+"&"+this.endDate+"/limitL&"+this.lessLimit;
        console.log(path);
        axios.get(path)
          .then(response => {
            this.countLesslimit = response.data.data.length;
            console.log(this.countLesslimit);
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },

    getDataBadUpper() {
      this.dateList= [];
      this.dataList = [];
      if(this.Lselected!= '' && this.Fselected != '' ){
        console.log(this.upperLimit);
        this.startDate = moment(this.startDate).format('YYYY-MM-DD');
        this.endDate = moment(this.endDate).format('YYYY-MM-DD');
        let path = "http://localhost:2000/api/"+this.Lselected+"/"+this.Fselected+"/"+this.startDate+"&"+this.endDate+"/limitU&"+this.upperLimit;
        console.log(path);
        axios.get(path)
          .then(response => {
            this.countGreatlimit= response.data.data.length;
            console.log(this.countGreatlimit);
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },

    

  },
  async created() {
      this.getLineList();
      this.getLineFilters();
      this.getFilterSensorLimits();
      this.getFilterDataBetweenDates();
      this.getDatesMinMax();
      this.getDataMinMaxMed();
      this.getDataBadLowerd();
      this.getDataBadUpper();

  },

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1 {
  margin: 40px 0 0;
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
#dataP {
  margin: 400px 0 0;
}
</style>
