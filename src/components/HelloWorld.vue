<template>
  <div>
    <el-row class="warp">
      <el-row :gutter="20" class="main-title">
        <!--标题-->
        <el-col :span="6">
          <i class="title-mark"></i>
          <h2 class="title-txt">文章编辑</h2>
        </el-col>
        <!--功能键-->
        <el-col :span="18"></el-col>
      </el-row>
      <!-- Form 组件提供了表单验证的功能，只需要通过 rule 属性传入约定的验证规则，并 Form-Item 的 prop 属性设置为需校验的字段名即可。具体可以参考官网：http://element.eleme.io/#/zh-CN/component/form -->
      <el-col :span="24" class="warp-main">
        <el-form ref="infoForm" :model="infoForm" :rules="rules" label-width="120px">
          <el-form-item label="标题" prop="title">
            <el-input v-model="infoForm.title"></el-input>
          </el-form-item>
          <el-form-item label="副标题" prop="subTitle">
            <el-input v-model="infoForm.subTitle"></el-input>
          </el-form-item>
          <el-form-item label="时间" prop="time">
            <el-input v-model="infoForm.time"></el-input>
          </el-form-item>
          <el-form-item label="开头" prop="start">
            <el-input v-model="infoForm.start"></el-input>
          </el-form-item>
          <el-form-item label="结尾" prop="end">
            <el-input v-model="infoForm.end"></el-input>
          </el-form-item>
          <el-form-item label="底部时间" prop="endTime">
            <el-input v-model="infoForm.endTime"></el-input>
          </el-form-item>
          <div v-for="(item,index) in content" v-if="content">
                <el-form-item label="图片"  v-if="item.src">
                  <div class="flex">
                    <img :src="item.src" class="rendersrc"/>
                    <el-button class="del" @click="delContent(index)">删除</el-button>
                  </div>
                </el-form-item>
                <el-form-item label="段落" v-if="item.text !== undefined">
                  <div class="flex">
                    <el-input type="textarea" v-model="item.text"></el-input>
                    <el-button class="del" @click="delContent(index)">删除</el-button>
                  </div>
                </el-form-item>
                <el-form-item label="小标题" v-if="item.subheading !== undefined">
                  <div class="flex subheading">
                    <el-input v-model="item.subheading"></el-input>
                    <el-button class="del" @click="delContent(index)">删除</el-button>
                  </div>
                </el-form-item>
          </div>
          <div class="footer">
            <el-button type="primary" @click="onAddTXT">添加段落</el-button>
            <el-button type="primary" @click="onAddTitle">添加小标题</el-button>
            <label class="img el-button el-button--primary">
                添加图片
                <input type="file" @change="upimg" />
            </label>
          </div>
          <el-form-item>
            <el-button type="primary" @click="onSubmit">最终确认提交</el-button>
          </el-form-item>
        </el-form>
      </el-col>
    </el-row>
  </div>
</template>

<script>
  // 时间获取函数
  Date.prototype.Format = function (fmt) {
    var o = {
        "M+": this.getMonth() + 1, // 月份
        "d+": this.getDate(), // 日
        "h+": this.getHours(), // 小时
        "m+": this.getMinutes(), // 分
        "s+": this.getSeconds(), // 秒
        "q+": Math.floor((this.getMonth() + 3) / 3), // 季度
        "S": this.getMilliseconds() // 毫秒
    };
    if (/(y+)/.test(fmt))
        fmt = fmt.replace(RegExp.$1, (this.getFullYear() + "").substr(4 - RegExp.$1.length));
    for (var k in o)
        if (new RegExp("(" + k + ")").test(fmt)) fmt = fmt.replace(RegExp.$1, (RegExp.$1.length == 1) ? (o[k]) : (("00" + o[k]).substr(("" + o[k]).length)));
            return fmt;
  }

  export default {
    data() {
      return {
        infoForm: {
          title: '',  // 标题
          subTitle: '',  // 副标题
          time: new Date().Format("yyyy-MM-dd"),
          start: '', // 开头
          end: '', // 结尾
          endTime: '', // 底部时间
        },
        content: [], // 内容
        editorOption:{
          modules:{
              toolbar:[
                    ['image']
              ]
          }
        },
        //表单验证
        rules: {
          title: [
            {required: true, message: '请输入标题', trigger: 'blur'}
          ],
        },
      }
    },
    methods: {
      onEditorReady(editor) {
      },
      // 修改
      delContent (index) {
        this.content.splice(index, 1)
      },
      onAddTXT () {
        this.content.push(
          {
            type: 'text',
            text: ''
          }
        )
      },
      onAddTitle () {
        this.content.push(
          {
            type: 'subheading',
            subheading: ''
          }
        )
      },
      onEditorChange (e) {
        const { html } = e
        const imgReg = /<img.*?(?:>|\/>)/gi;
        const srcReg = /src=[\'\"]?([^\'\"]*)[\'\"]?/i;
        const arr = html.match(imgReg)
        const currentImg = arr[arr.length -1]
        const src = currentImg.match(srcReg)
        this.content.push(
          {
            type: 'image',
            src: src[1]
          }
        )
      },
      upimg(e) {
       const _self = this;
       var files = e.target.files[0];
       if (!e || !window.FileReader) return; // 看支持不支持FileReader
       let reader = new FileReader();
       reader.readAsDataURL(files); // 这里是最关键的一步，转换就在这里
       reader.onloadend = function() {
        _self.content.push(
          {
            type: 'image',
            src: this.result,
          }
        )
       };
     },
      onSubmit() {
        this.$refs.infoForm.validate((valid) => {
          if(valid) {
            const parmas = {
              ...this.infoForm,
              content: this.content,
            }
            console.log('parmas :', parmas)
            this.$axios.post('/add', parmas)
              .then(res => {
                if(res.errCode == 200) {
                  alert('上传成功', JSON.stringify(parmas))
                }
              })
              .catch(e => alert(JSON.stringify(parmas)))
          }
        });
      },
    },
  }
</script>

<style>
  .ql-toolbar {
      background-color: #409EFF;
      border-color: #409EFF;
      display: flex;
      width: 45px;
  }
  .flex {
    display: flex;
  }
  .del {
    margin-left: 10px;
  }
  .ql-container {
    display: none;
  }
  .footer {
    display: flex;
    margin: 0 auto;
    margin-bottom: 40px;
    margin-left: 120px;
  }
  .footer button {
    margin-right: 10px
  }
  .img {
    width: 114px;
    margin-left: 40px;
  }
  .img input {
    opacity: 0;
  }
  .rendersrc {
    max-height: 200px;
  }
  .subheading input {
    font-weight: bold;
  }

</style>
