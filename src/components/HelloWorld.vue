<template>
  <div class="hello">

    <input @change="fileChange" type="file" id="file_input_expense" name="file_input_expense">
    <div class="wrapper">
      <div class="row" v-for="(data, key) in csvData" v-bind:key="data.id">
        <div class="col" v-bind:class="{active: key}" v-for="(d, k) in data"  v-bind:key="d.id">
          {{k}}: {{d}}
        </div>
      </div>      
    </div>
  </div>
</template>

<script>
/* eslint-disable */
var Encoding = require('encoding-japanese');
const axios = require('axios');

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data: function() {
    return {
      headers: [
        {
          text: "Code",
          align: "left",
          sortable: false,
          value: "code"
        },
        { text: "Name", align: "left", value: "name" },
        { text: "WorkerType", align: "left", value: "workerType" }
      ],
      csvData: [],
      path: './data/sampledata.txt'
    };
  },
  created: function() {
    this.loadFile();
  },
  methods: {
    /*
      csvの変換
    */
    loadFile: function() {
      let path = this.path;
      axios.get(path)
        .then(function (response) {
          console.log(response);
        })
        .catch(function (error) {
          // handle error
          console.log(error);
        })
        .then(function () {
          // always executed
        });

    },
    /*
      フォームからアップロードされたCSVを配列に変換
    */
    fileChange: function(e) {
      const self = this
      self.csvData = []
      const file = e.target.files[0];
      const reader = new FileReader();
      reader.onload = function(e) {
        // csvテキストをここで取得
        var codes = e.target.result;
        // 日本語文字化けの修正
        var encoding = Encoding.detect(codes);
        var unicodeString = Encoding.convert(codes, {
           to: 'unicode',
           from: encoding,
           type: 'string'
        });
        const lines = unicodeString.trim().replace(/"/g, "").split("\n");
        lines.forEach(element => {
          const dataArr = element.split(",");
          const row = [];
          if(dataArr.length > 0){
            for (var i = 0; i < dataArr.length; i++) {
              row.push(dataArr[i])
            }
            self.csvData.push(row);
          }
        });
      };
      reader.readAsBinaryString(file);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.row{
  display: -ms-flexbox;
  display: flex; 
}
.col{
    display: -ms-flexbox;
  display: flex;  
  justify-content: center;
  -webkit-justify-content: center;
  -ms-flex-pack: center;
  align-content: center;
  -webkit-align-content: center;
  -ms-flex-line-pack: center;
  align-items: center;
  -webkit-align-items: center;
  -ms-flex-align: center;
  text-align: center;  
}
h3 {
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
</style>
