<template>
  <div class="loadding" v-loading="loading">

     <div class="header">
          <div class="juedingle">今天吃：{{chi}}</div>
            <el-button class="button-new-tag" size="small" :type="types" @click="chiyige">{{content}}</el-button>
         
     </div>
 <div class="eatWhat">
                 
      <el-input class="input-new-tag" v-if="inputVisible" v-model="inputValue" ref="saveTagInput" size="small"
        @keyup.enter.native="handleInputConfirm" @blur="handleInputConfirm" placeholder="新发现">
      </el-input>
 <el-button v-else class="button-new-tag" size="small" @click="showInput">添&nbsp;&nbsp;&nbsp;加</el-button>
      <el-tag :key="tag" v-for="tag in dynamicTags" closable :disable-transitions="false" @close="handleClose(tag)">
        {{tag}}
      </el-tag>

    </div>
    
  </div>
</template>

<script>
  var Host = 'http://106.14.212.56:8080/happy/';
  export default {
    name: 'HelloWorld',
    data() {
      return {
		  dynamicTags: [],
        inputVisible: false,
        inputValue: '',
		  loading: true,
      qiguai:'',
      chi:'',
      num: 0,
      content:'随机选一个',
      types:'primary',
      }
    },
    mounted() {
      this.axios.get(Host + 'queryTable?db=SF_foods&table=foods').then(response => {
          let json = response.data.data[0];
          console.log("%c 初始查询表的数据", "color:red", json.foodName)
          this.dynamicTags = json.foodName;
          this.loading = false;
			 this.qiguai = JSON.stringify(this.dynamicTags) 
        })
        .catch((error) => {
          console.log(error)
        })

    },
    methods: {
      handleClose(tag) {
        this.dynamicTags.splice(this.dynamicTags.indexOf(tag), 1);
		  this.change();
      },

      showInput() {
        this.inputVisible = true;
        this.$nextTick(_ => {
          this.$refs.saveTagInput.$refs.input.focus();
        });
      },

      handleInputConfirm() {
        
        let inputValue = this.inputValue;
        if (inputValue) {
			 this.dynamicTags.unshift(inputValue);
		  }

        this.inputVisible = false;
        this.inputValue = '';
			this.change();
		},
		// 数据库修改新菜
		change(){
      console.log("%c 修改数据","color:blue",this.qiguai, this.dynamicTags)
      this.loading = true;
        let postData = {
          "db": "SF_foods",
          "table": "foods",
          "query": {
            "key": "foodName",
            "value": JSON.parse(this.qiguai)
          },
          "jsonMessage": {
            "key": "foodName",
            "value": this.dynamicTags
          },


        }
        this.axios.post(Host + 'updata/', postData).then(response => {
            console.log(response)
             
            this.axios.get(Host + 'queryTable?db=SF_foods&table=foods').then(response => {
              let json = response.data.data[0];
              console.log("%c 初始查询表的数据", "color:red", json.foodName)
              this.dynamicTags = json.foodName;
              this.loading = false;
                this.qiguai = JSON.stringify(this.dynamicTags) 
                this.loading = false;
                  })
                  .catch((error) => {
                    console.log(error)
                  })
          })
          .catch((error) => {
            console.log(error)
          })
		},
    chiyige(){
      var Arr = this.dynamicTags
      var n = Math.floor(Math.random() * Arr.length + 1)-1;  
      // console.log(n)
      this.chi = this.dynamicTags[n];
      // console.log(this.dynamicTags,this.chi)
      this.num ++;
      console.log(this.num)
      if(this.num == 3){
        this.types = 'danger';
        this.content = '这么矫情，就吃这个！'
      } else if (this.num > 3){
        this.types = 'primary';
        this.content = '你点吧，反正禁止你点也会刷新网页'
      }
    }

    },
  }

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .loadding {
    width: 100vw;
  }

  .eatWhat {
    padding: 2rem 1.5rem;
  }

  .el-tag+.el-tag {
    margin: 0.5rem;
  }

  .button-new-tag {
        min-width: 75px;
    height: 32px;
    line-height: 30px;
   padding-top: 0;
     padding-bottom: 0;
  }

  .input-new-tag {
    width: 75px;
    vertical-align: center;
  }

  body {
    margin: 0;
  }
  .juedingle {
    margin-top: 1rem;
    margin-bottom: 1rem;
    font-weight: 600;
  }
  .header {
     border: 1px solid #cccccc;
    padding-bottom: 1rem;
    width: 70vw;
    margin: 0 auto;
    margin-top: 2rem;
  }

</style>
