<template>
        <el-row class="ueb-search m20">
            <el-form
                size="mini"           
                class="form-control"
                v-bind="$attrs"
                v-on="$listeners"
                :inline="true"
                style="width:100%;"
                ref="searchForm">
                <slot>
                    <!-- <custom-condition 
                        ref="customCondition1"
                        :support="support1"
                        :is-remove="false"
                        :max="3" 
                        field-style="max-width:23%;"
                        value-style="width:200px" >
                    </custom-condition> -->
                    <ueb-condition :data="data" 
                        v-if="filtrateType === 2"
                        width="240"
                        @change-field="changeField" 
                        @remove-field="removeField"
                        :max="2"  
                        value-style="width:200px" 
                        field-style="max-width:23%;"/>
                    <span v-else>
                        <el-form-item 
                            v-for="(item,index) in searchcolumns"
                            :key="'ElForm___'+index" >
                            <ueb-redact 
                                :deploy="item"
                                inline
                                :options="options"
                                @change="(val)=>handleChange(val,item)"
                                v-model="model[item.id]">
                            </ueb-redact>
                        </el-form-item>  
                    </span>    
                </slot>
                <el-form-item class="btns">
                   <span v-for="item in support.btns">
                        <el-popover
                            v-if="item.popover !== ''"
                            :key="item.id"
                            placement="bottom"
                            width="500"
                            v-model="filtrateDisabled"
                            ref="filtrateRef"
                            :width="filtrateType !== 2?800:500"
                            trigger="click">
                            <span v-if="$slots.filtrate">
                                <slot name="filtrate"></slot>
                            </span>
                            <span v-else>
                                <div v-if="filtrateType !== 2">
                                    <el-row v-if="isFiltrate">
                                        <el-col :span="3" style="background-color: #1e263a;height: 280px;">
                                            <ul class="ueb-tab__menus">
                                                <li :class="{active:btnType==='default'}" @click="skip('default')">常用筛选</li>
                                                <li @click="skip('search')" :class="{active:btnType!=='default'}">搜索</li>
                                            </ul>
                                        </el-col>
                                        <el-col :span="21">
                                            <el-form  size="mini"
                                            @scroll.native="paperScroll" 
                                            style="height: 200px;overflow: auto; overflow-x: hidden;margin-left: 20px;" 
                                            label-position="top">
                                                <el-col :span="8"
                                                     v-for="(item,index) in support.filtrates" 
                                                     :key="'filtrates'+item.id" 
                                                     :ref="'anchor-'+index"
                                                     style="margin-top: 10px;" >
                                                    <el-form-item :label="item.name" >
                                                        <!--v-if="typeof item.options !== 'undefined'"-->
                                                        <span v-if="typeof item.slot!=='undefined'&&item.slot!==null&&item.slot !=='' && !$slots[item.slot]">
                                                            <slot name="item.slot"></slot>
                                                        </span>
                                                        <ueb-redact 
                                                            value-style="width:200px" 
                                                            :width="200"
                                                            v-else
                                                            :deploy="item"
                                                            :options="options"
                                                            @change="(val)=>handleChange(val,item)"
                                                            v-model="model[item.id]">
                                                        </ueb-redact>
                                                        <!-- <el-select style="width: 200px;" 
                                                            :placeholder="item.placeholder||'请选择'" 
                                                            :key="'selected-'+index" 
                                                            v-model="model[item.id]" 
                                                            :filterable="item.search" 
                                                            v-else-if=" item.type === 'selected' || (typeof item.options!=='undefined' && item.options !== false) ">
                                                            <el-option
                                                                v-for="(o,oi) in getOptions(item.options,item.id,item)"
                                                                :key="'selected'+oi"
                                                                :label="o.label"
                                                                value-key="value"
                                                                :value="o.value">
                                                            </el-option>
                                                        </el-select>
                                                        <el-input style="width: 200px;" 
                                                            v-model="model[item.id]" 
                                                            :placeholder="item.placeholder||'请输入值'" 
                                                            v-else-if = "item.type === 'number'"></el-input>
    
                                                        <el-date-picker style="width: 200px;"
                                                            v-else-if = "dateType.indexOf(item.type)!==-1"
                                                            :key="'date_'+index"
                                                            v-model="model[item.id]"
                                                            :type="item.type"
                                                            :placeholder="item.placeholder||'选择日期'"
                                                            v-bind="getColBind(item)">
                                                        </el-date-picker>   
    
                                                        <el-input v-model="model[item.id]" 
                                                            type="text" 
                                                            style="width: 200px;" 
                                                            :placeholder="item.placeholder||'请输入值'" 
                                                            v-else>
                                                        </el-input> -->
                                                    </el-form-item>
                                                </el-col> 
                                            </el-form>
                                            <div style="word-break:keep-all;word-wrap:normal;float:right;margin-right:20px;margin-top:20px;">
                                                <el-button @click="handleClick('reset')">重置</el-button>
                                                <!-- <ueb-btn-confirm type="primary" @click="handleClick('search')">查询</ueb-btn-confirm> -->
                                                <confirm-button @click="handleClick('search')"></confirm-button>
                                            </div>
                                        </el-col>
                                    </el-row>  
                                </div>
                                <div v-else>
                                    <el-form ref="form" size="mini" style="margin-left:5px;margin-top:20px;height: 200px;overflow: auto;overflow-x: hidden;">
                                        <!-- <custom-condition 
                                            ref="customCondition2"
                                            :support="support1"
                                            addible 
                                            value-style="width:200px">
                                        </custom-condition> -->
                                        <ueb-condition :data="data"
                                            @change-field="changeField" 
                                            @remove-field="removeField" 
                                            value-style="width:200px" 
                                        field-style="max-width:95%;" addition/>
                                    </el-form>
                                    <el-button type="text"  size="mini" icon="el-icon-plus" @click="add">添加条件</el-button>
                                    <div style="word-break:keep-all;word-wrap:normal;margin-left: 50%;">
                                        <el-button>重置</el-button>
                                        <confirm-button></confirm-button>
                                    </div>
                                </div>
                            <!--两种格式-->
                            </span>
                            <el-button slot="reference" type="text" size="mini" :type="item.type" :icon="item.icon" style="margin-right: 10px;" @click="filtrateClick">{{item.name}}</el-button>
                        </el-popover>
                        <confirm-button
                            v-else-if="item.id === 'search'"
                            size="mini"
                            :key="item.id"
                            :icon="item.icon"
                            @click="handleClick('search')">
                            
                        </confirm-button>
                        <el-button v-else 
                            :type="item.type"
                            :icon="item.icon"
                            :key="item.id"
                            size="mini"
                            style="margin-right: 10px;"
                            @click="handleClick(item.id)">
                            {{item.name}}
                        </el-button>
                   </span>
                </el-form-item>
            </el-form>
            <el-col class="labels" v-if="searchTag.length > 0" >
                <div class="labels_l">
                        <!-- label 标签-->
                        <el-tag
                            v-for="tag in searchTag"
                            :key="tag.name"
                            closable
                            size="small"
                            @close="clearTag(tag)"
                            type="info">
                            <el-tooltip class="item" effect="dark" :content="getTagName(tag.value,true)" placement="top-start">
                                <span class="tag-title">{{tag.name}} : <span class="tag-value" v-text="tag.mark||'' + getTagName(tag.value,false)"></span></span>
                            </el-tooltip>
                        </el-tag>
                        <el-button type="text" v-if="searchTag.length > 0" @click="clearTag('all')">清除</el-button>
                </div>
            </el-col>
            <!-- 批量查询弹出框 -->
            <el-dialog title="批量查询" 
                :visible.sync="visible"
                ref="batchDialog"
                class="batchs"
                width = '550px'
                :before-close="cancel"
                append-to-body>
                <div class="multiple_choice">
                    <el-radio-group v-model="searchObj.fields">
                        <el-radio 
                             v-for="item in support.batchs"
                            :key = "item.id" 
                            :label = "item.id">
                            <span class="radio-title" style="width:100px;overflow:hidden;">按{{item.name}}</span>
                        </el-radio>
                    </el-radio-group>
                </div>
                <div class="tips">
                    每行一个值(支持excel复制黏贴)
                </div>
                <span class="division"></span>
                <el-input
                    type="textarea"
                    :rows="14"
                    :autosize="false"
                    placeholder="请输入内容"
                    v-model="searchObj.context">
                </el-input>
                <div slot="footer" class="dialog-footer">
                    <el-button size="medium" @click="cancel">取 消</el-button>
                    <!-- <el-button type="primary" @click="batchSearch">查询</el-button> -->
                    <confirm-button size="medium" @click="batchSearch"></confirm-button>
                </div>  
            </el-dialog>     
        </el-row>
    
    </template>
    
    <script>
        import ElForm from 'element-ui/lib/form'
    
        import SearchData from './dataSupport.js'
    
        import Support from './../Tab/support.js'
    
        import customCondition from './../Tab/customCondition'
    
        import UebCondition from './condition'
    
         import uebRedact from './../Table/input.vue'
    
        import ConfirmButton from './../button/SelectButton.vue'
    
        export default{
            name:'ueb-search',
            components:{customCondition,UebCondition,ConfirmButton,uebRedact},
            props:{
                ...ElForm.props,
                btns:{
                    type:Array,
                    default:()=>['reset','search']
                },
                search_label:{
                    type:Boolean,
                    default:false
                },
                queryOptions:{
                    type:[Array],
                    default:()=>[]
                },
                transformFields:{
                    type:Function
                },
                model:{
                    type:[Object,Array],
                    default:()=>{return {}}
                },
                filtrateType:{
                    type:[String,Number],
                    default:1
                },
                options:{
                    type:Object,
                    default:()=>{return {}}
                },
                maxSearch:{
                    type:Number,
                    default:6
                },
                maxBatch:{
                    type:Number,
                    default:1000
                },
                initSearch:{
                    type:Boolean,
                    default:false
                }
            },
            data(){
                return { 
                    searchTag:[],
                    support: new SearchData(this.queryOptions,this.model,this.filtrateType),
                    visible:false,
                    searchObj:{
                        fields:'',
                        context:''
                    },
                    record:'',
                    data:[],
                    dateType:['year','month','date','dates','week','datetime','datetimerange','daterange'],
                    btnType:'default',
                    isFiltrate:false,
                    filtrateDisabled:false
                }     
            },
            created(){
                this.recordSearchObj = Object.assign({},this.searchObj)
                this.batchBackups = this.recordSearchObj       
            }, 
            mounted(){
                if(this.filtrateType !== '1' || this.filtrateType !== 1){
                    this.ds = new Support(this.queryOptions,[],'')
                    this.ds.addCondition(this.data)
                } 
                //初始化model
                if(this.initSearch){
                    this.search()
                }
                let model = this.model||{}
                this.modelCache = Object.assign({},model)
            },
            computed:{
                searchcolumns(){
                    let arr = []
                    if(Array.isArray(this.queryOptions)){
                        for(let i in this.queryOptions){
                            if(i>=this.maxSearch){
                                break
                            }
                            let obj = this.queryOptions[i]
                            arr.push(Object.assign({},obj,{placeholder:obj.placeholder||obj.name}))
                        }
                    }
                    return arr
                },
                //生成的标签
                tags(){
    
                }
            },
            methods:{
                handleChange(val,item){
                    let {transition} = item
                    if(typeof transition === 'function'){
                        let obj = transition(val)
                        if(this.support._isJson(obj)){
                            Object.assign(this.support.model,obj)
                        }
                    }
                },
                filtrateClick(){
                    if(!this.isFiltrate){
                        this.isFiltrate=true
                    }
                },
                skip(type){
                    this.btnType = type   
                },
                paperScroll(e){
                    //console.log(123,e.scrollTop)
                },
                getColBind(col){
                    const bind = Object.assign({}, col)
                    delete bind.options
                    delete bind.name
                    delete bind.id
                    delete bind.batch
                    delete bind.tags
                    delete bind.filtrate
                    return bind
                },
                getOptions(e,value,data){
                    let result = []
                    if(this.support._isFunction(e)){
                        result = e()
                        if(!Array.isArray(result)){
                            result=[]
                        }
                    }else if(Array.isArray(e)){
                        result = e
                    }else if(this.support._isString(e) && e!=='true'&&e!=='false'){
                        //
                        result = this.options[e]||[]
                    }else if(typeof e === 'boolean'){
                        result = this.options[value]||[]
                    }
                    return result
                },
                getTagName(value,flag){
                    if(flag === true){
                        return value
                    }
                    let result =  this.cutString(value,18)
                    return result
                },    
                cutString(str, len) {
                    if(str.length <= len) {
                        return str;
                    }
                    var strlen = 0;
                    var s = "";
                    for(var i = 0;i < str.length; i++) {
                    s = s + str.charAt(i);
                    if (str.charCodeAt(i) > 128) {
                    strlen = strlen + 2;
                    if(strlen >= len){
                        return s.substring(0,s.length-1) + "...";
                    }
                    } else {
                    strlen = strlen + 1;
                    if(strlen >= len){
                        return s.substring(0,s.length-2) + "...";
                    }
                    }
                }
                return s;
                }, 
                changeField(){
                    this.data = this.ds.fieldChange(this.data)
                },
                removeField(obj){
                    this.data = this.ds.removeCondition(this.data,obj)
                },  
                add(){
                    try{
                        this.ds.addCondition(this.data)
                    }catch(e){
                        this.$message.error(e)
                    }    
                },  
                clearTag(obj){
                    this.searchTag = this.support.clearTag(this.searchTag,obj,this.model)
                    this.$emit('handle-search','search',this.model)
                }, 
                reset(){
                    Object.assign(this.batchBackups,this.recordSearchObj)
                    Object.assign(this.searchObj,this.batchBackups);
                    Object.assign(this.model,this.modelCache)
                    this.searchTag = this.support.clearTag(this.searchTag,'all',this.model)
                    //this.modelCache
                    this.$emit('input',this.model)
                    this.$emit('handle-search','reset',this.model)
                },
                handleClick(id){
                    var that = this;
                    this.record = id
                    this.filtrateDisabled = false 
                    //按钮点击事件
                    switch(id){
                        case 'reset':
                            this.reset()
                            break;
                        case 'search':
                            that.search()
                            break;
                        case 'batch':
                            that.batch()
                            break;
                        default:
                            break;
                    }
                }, 
                transformOptionsFields(id,val){
                    let arr = this.options[id]
                    if(Array.isArray(arr)){
                        for(let i in arr){
                            let {label,value} = arr[i]
                            if(value == val){
                                return label||val
                            }
                        }
                    }
                },
                search(){
                    Object.assign(this.batchBackups,this.recordSearchObj)
                    Object.assign(this.searchObj,this.batchBackups);
                    //transition
                    let func = this.transformFields
                    if(typeof this.transformFields !== 'function'){
                        func = this.transformOptionsFields
                    }
                    this.searchTag = this.support.builderTag(this.model,func,this.options)

                    //查询
                    this.$emit('handle-search','search',this.model)
                },
                batch(){
                    var fields = this.searchObj.fields;
                    if(this.support._isEmpty(fields)){
                        //获取默认值 (第一个)
                       if(this.support.batchs.length>0){
                            let obj = this.support.batchs[0];
                            this.searchObj.fields = obj.id
                        }
                    }  
                    this.batchBackups = Object.assign({},this.searchObj);           
                    //TODO 页面初始化
                    this.visible = true;
                },
                cancel(){
                    Object.assign(this.searchObj,this.batchBackups);
                    this.visible = false;
                },
                filtrate(){
                    //筛选
                },
                trim(s){
                    if(Object.prototype.toString.call(s) === '[object String]'){
                        return s.replace(/(^\s*)|(\s*$)/g, "")
                    }
                    return s.replace(/(^\s*)|(\s*$)/g, "")
                },
                batchSearch(){
                    //批量查询的时候清空标签
                    let sc = this.trim(this.searchObj.context)
                    if(typeof sc === 'undefined' || sc === null || sc === ''){
                        this.$message.error('您还没有输入查询信息')
                        return 
                    }
                    this.searchTag = this.support.clearTag(this.searchTag,'all',this.model)
                    this.$emit('input',this.model)
                    let func = this.transformFields
                    if(typeof this.transformFields !== 'function'){
                        func = this.transformOptionsFields
                    }
                    this.searchTag = this.support.builderTag(this.model,this.transformFields,this.options)
                    let obj={}
                    let context =sc.replace(/\n/g,',')
                    let fields = this.searchObj.fields
                    let vs = context.split(',')
                    //自动转换为对应的值
                    let arr = this.options[fields]
                    if(Array.isArray(arr)){
                        let strs=[]
                        vs.forEach(item => { 
                            let str = item
                            for(let i in arr){
                                let object = arr[i]
                                let {label,value} = object
                                if(label == item){
                                    str = value
                                    break
                                }
                            }
                            strs.push(str)
                        }); 
                        context = strs.join(',')
                    }
                    let arr1 = context.split(',')
                    if(Array.isArray(arr1)&&arr1.length>0){
                        let result = []
                        arr1.forEach(item=>{
                            if(typeof item !=='undefined' && item !== null && item!==''){
                                result.push(this.trim(item))
                            }
                        })
                        if(result.length<=0){
                            this.$message.error('您还没有输入查询信息')
                            return 
                        }
                        if(result.length>this.maxBatch){
                            this.$message.error('批量查询最多只能查询'+this.maxBatch+'条数据哦');
                            return
                        }
                        obj[fields]=result.join(',')
                        this.$emit('handle-search','batch',obj)
                        this.visible = false;
                    }else{
                        this.$message.error('您还没有输入查询信息')
                    } 
                },
                fieldChange(){
    
                }
            }
        }
    </script>
    
    <style lang="less" >
        
        .ueb-search{
            width:100%;
            margin-top:10px;
            border-bottom:1px solid #e4eaec;
            .el-form--inline{
                float:left;       
            }
            .labels{
                width:98%;
                margin-bottom:20px;
                .labels_l{
                    float:left;
                    .qyzt{
                        height:28px;
                        color:#a3aab7;
                        border:1px solid #dcdfe6;
                        padding:0 30px 0 10px;
                        max-width:350px;
                        display:inline-block;
                        overflow: hidden;
                        text-overflow:ellipsis;
                        white-space: nowrap;
                        position: relative;
                        span{
                            color:#373e4f;
                            text-align:left;
    
                        }
                        .el-icon-close{
                            position:absolute;
                            right:10px;
                        }
                    }
                }
            }
            .main_m{
                width:98%;
                overflow: hidden;
                .main_l{
                    float:left;
                    margin-top:20px;
                    span{
                        font-size:14px;
                        padding:0 5px;
                        cursor: pointer;
                        &:hover {
                            color: #409EFF;
                        }
                    }
                    .border_r{
                        border-right:1px solid #dcdfe6;
                        padding-right:8px;
                    }
    
                }
                .main_r{
                    float:right;
                    .el-dropdown{
                        float: right;
                        margin-right:15px;
                        margin-top:11px;
                    }
                }
                
            }
    
            .el-tag{
                margin-right:10px;
                background-color: white;
                border-color:#ecf1f4;
                .tag-title{
                    color: #8d92a1;
                }
                .tag-value{
                    color: #1e232b;
                }
                
            }
        }
        .m20{
            margin:20px 20px 0 20px;
           
        }
       
    
        .ueb-tab__menus li{
            line-height: 45px;
            height: 45px;
            font-size: 15px;
            text-align: right;
            padding-right: 10px;
            cursor:pointer;
            color: #b3bccf;
        }
    
        .ueb-tab__menus li.active{
            background-color: #2d374f;
            color: #ffffff;
        }
        .batchs{
            .multiple_choice{
                width:100%;
                overflow: hidden;
                margin-bottom: 5px;
                .radio-title{
                    margin-right:20px;
                    overflow:hidden; 
                    text-overflow:ellipsis; 
                    white-space:nowrap; 
                    word-break:keep-all;
                }
                .el-radio{
                    margin-bottom: 10px;
                   
                }
            }
            .tips{
                margin-bottom:8px;
            }
            .el-textarea__inner{
                resize:none;
            }
        }
    </style>    