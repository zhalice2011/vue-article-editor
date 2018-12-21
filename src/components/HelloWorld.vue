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
            <el-form-item label="新闻类型" prop="newsType">
                <el-select v-model="infoForm.newsType">
                    <el-option
                        v-for="item in options"
                        :key="item.value"
                        :label="item.label"
                        :value="item.value">
                    </el-option>
                </el-select>
            </el-form-item>
            <el-form-item label="展示渠道" prop="channel">
                <el-select v-model="infoForm.channel">
                    <el-option
                        v-for="item in channels"
                        :key="item.value"
                        :label="item.label"
                        :value="item.value">
                    </el-option>
                </el-select>
            </el-form-item>
            <el-form-item label="标题" prop="title">
                <el-input v-model="infoForm.title"></el-input>
            </el-form-item>
            <el-form-item label="副标题" prop="subTitle">
                <el-input v-model="infoForm.subTitle"></el-input>
            </el-form-item>
            <el-form-item label="时间" prop="time">
                <el-input v-model="infoForm.time"></el-input>
            </el-form-item>
            <el-form-item label="新闻开头问候语" prop="beginHello">
                <el-input v-model="infoForm.beginHello"></el-input>
            </el-form-item>
            <el-form-item label="新闻结尾" prop="ending">
                <el-input v-model="infoForm.ending"></el-input>
            </el-form-item>
            <el-form-item label="底部落款时间" prop="endTime">
                <el-input v-model="infoForm.endTime"></el-input>
            </el-form-item>
            <el-form-item label="封面内容简介">
                <el-input v-model="infoForm.summary"></el-input>
            </el-form-item>
            <el-form-item label="新闻缩略图">
                <label class="cover img el-button el-button--primary" v-if="infoForm.newsThumb === ''">
                    新闻缩略图
                    <input type="file" @change="upCover" v-if="infoForm.newsThumb === ''" />
                </label>
                <div class="flex flex-column" v-if="infoForm.newsThumb !== ''">
                    <img :src="infoForm.newsThumb" class="rendersrc"/>
                    <el-button class="imgdel imgdel" @click="delCover()">删除</el-button>
                </div>
            </el-form-item>
            <el-form-item label="是否置顶" prop="setTop">
                <el-select v-model="infoForm.setTop">
                    <el-option
                        v-for="item in setTops"
                        :key="item.value"
                        :label="item.label"
                        :value="item.value">
                    </el-option>
                </el-select>
            </el-form-item>
            <el-form-item label="状态" prop="channel">
                <el-select v-model="infoForm.status">
                    <el-option
                        v-for="item in statuss"
                        :key="item.value"
                        :label="item.label"
                        :value="item.value">
                    </el-option>
                </el-select>
            </el-form-item>
            <div v-for="(item,index) in content"  v-if="content" >
                    <el-form-item label="图片" v-if="item.src !== undefined">
                    <div class="flex flex-column">
                        <img :src="item.src" class="rendersrc"/>
                        <el-button class="imgdel" @click="delContent(index)">删除</el-button>
                    </div>
                    </el-form-item>
                    <el-form-item label="段落" v-if="item.text !== undefined">
                    <div class="flex">
                        <el-input type="textarea" v-model="item.text"></el-input>
                        <el-button class="del" @click="delContent(index)">删除</el-button>
                    </div>
                    </el-form-item>
                    <el-form-item label="小标题"  v-if="item.subheading !== undefined">
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
                    <input type="file" @change="upimg" id="image" />
                </label>
            </div>
            <el-form-item>
                <el-button type="primary"  @click="onSubmit">最终确认提交</el-button>
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
                newsType: '1', // 新闻类型 默认百姓公告
                channel: '1', // 展示渠道 默认pc
                title: '',  // 标题
                subTitle: '',  // 副标题
                time: new Date().Format("yyyy-MM-dd"), // 发布时间
                beginHello: '', // 开头
                ending: '', // 结尾
                endTime: '', // 底部时间
                summary: '', // 内容简介，封面内容
                newsThumb: '', // 封面缩略图
                setTop: '0', // 是否置顶【0-否，1-是】
                status: '1', // 状态【0-删除，1-正常】
            },
            options: [
                {
                    value: '1',
                    label: '百信公告'
                },
                {
                    value: '2',
                    label: '新闻动态'
                },
                {
                    value: '3',
                    label: '行业资讯'
                },
            ],
            channels: [
                {
                    value: '1',
                    label: 'PC'
                },
                {
                    value: '2',
                    label: '手机'
                },
            ],
            setTops: [
                {
                    value: '0',
                    label: '否'
                },
                {
                    value: '1',
                    label: '是'
                },
            ],
            statuss: [
                {
                    value: '1',
                    label: '正常'
                },
                {
                    value: '0',
                    label: '删除'
                },
            ],
            value: '',
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
        upimg(e) {
            debugger
            const _self = this;
            var files = e.target.files[0];
            if (!e || !window.FileReader) return; // 看支持不支持FileReader
            let reader = new FileReader();
            reader.readAsDataURL(files); // 这里是最关键的一步，转换就在这里
            reader.onloadend = function() {
                console.log('this.result :', this.result)
                _self.content.push(
                    {
                    type: 'image',
                    src: this.result,
                    }
                )
                document.getElementById('image').value = null;
            };
        },
        upCover(e) {
            const _self = this;
            var files = e.target.files[0];
            if (!e || !window.FileReader) return; // 看支持不支持FileReader
            let reader = new FileReader();
            reader.readAsDataURL(files); // 这里是最关键的一步，转换就在这里
            reader.onloadend = function() {
                _self.infoForm.newsThumb = this.result
            };
        },
        delCover() {
            this.infoForm.newsThumb = ''
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
    .cover {
        margin-left: 0px;
    }
    .img input {
        opacity: 0;
    }
    .rendersrc {
        max-width: 90%;
    }

    .subheading input {
        font-weight: bold;
    }
    .imgdel {
        width: 100px;
        margin-left: 10px;
    }

</style>
