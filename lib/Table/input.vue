<template>
    <span> 
        <span  v-if="isContains(dateType,deploy.type) && deploy.timePicker!==true" class="el_col_time_container" :class="{'data-inline':inline}">
            <span class="time_show_span" v-if="inline && typeof deploy.name !=='undefined' && deploy.name !== null && deploy.name !==''">{{deploy.name}}:</span>
            <el-date-picker
            :style="style1"
                v-model="val"
                :type="deploy.type"
                v-on="getListeners(deploy)"
                v-bind="getDatePickerBind(deploy)">
            </el-date-picker>
        </span>
        <span  v-else-if="isContains(dateType,deploy.type) && deploy.timePicker===true " class="el_col_time_container" :class="{'data-inline':inline}">
            <span class="time_show_span" v-if="inline && typeof deploy.name !=='undefined' && deploy.name !== null && deploy.name !==''">{{deploy.name}}:</span>
            <el-date-picker
                :style="style1"
                v-model="val"
                :type="deploy.type"
                v-on="getListeners(deploy)"
                v-bind="getDatePickerBind(deploy)">  
            </el-date-picker>
        </span>
        <span  v-else-if="deploy.timePicker===true" class="el_col_time_container" :class="{'data-inline':inline}">
            <span class="time_show_span" v-if="inline && typeof deploy.name !=='undefined' && deploy.name !== null && deploy.name !==''">{{deploy.name}}:</span>
            <el-date-picker
                :style="style1"
                v-model="val"
                :type="deploy.type"
                v-on="getListeners(deploy)"
                v-bind="getDatePickerBind(deploy)">
            </el-date-picker>
        </span>
        <el-radio v-else-if="isContains('radio',deploy.type)" v-model="val" v-bind="getRadioBind(deploy)" v-on="getListeners(deploy)" >{{deploy.title || ''}}</el-radio>
        <el-checkbox  v-else-if="isContains('radio',deploy.type)"  v-model="val" v-bind="getRadioBind(deploy)" v-on="getListeners(deploy)">{{deploy.title || ''}}</el-checkbox>
        <el-input v-else-if="isContains('number',deploy.type)"  v-model="val" v-bind="getBind(deploy)" v-on="getListeners(deploy)"></el-input>
        <el-switch v-else-if="isContains('switch',deploy.type)" v-model="val" v-bind="getBind(deploy)" v-on="getListeners(deploy)"></el-switch>
        <el-slider v-else-if="isContains('slider',deploy.type)" v-model="val" v-bind="getBind(deploy)" v-on="getListeners(deploy)"></el-slider>
        <el-rate v-else-if="isContains('rate',deploy.type)" v-model="val" v-bind="getBind(deploy)" v-on="getListeners(deploy)"></el-rate>
        <el-color-picker v-else-if="isContains('color',deploy.type)" v-model="val" v-bind="getBind(deploy)" v-on="getListeners(deploy)"></el-color-picker>
        <el-select v-else-if="isSelected(deploy)" v-model="val" v-bind="getSelectedBind(deploy)" v-on="getListeners(deploy)">
            <el-option
                v-for="item in getOptions(deploy)"
                :key="item.value"
                :label="item.label"
                :value="item.value">
            </el-option>
        </el-select>
        
        <el-input v-else  v-model="val" v-bind="getBind(deploy)" v-on="getListeners(deploy)"></el-input>
    </span>
</template>
<script>
    export default{
        name:'ueb-redact',
        props:{
            deploy:{
                type:Object,
            },
            value:'',
            options:{
                type:Object
            },
            inline:{
                type:Boolean,
                default:false
            },
            width:{
                type:[Number],
                default:0
            }
        },
        computed:{
            style1(){
                let style={}
                if(this.width>0){
                    style.width = this.width+'px'
                }
                if(this.inline){
                    style.border='none'  
                    style.width="240px"
                }
                
                return style
            }
        },
        data(){
            return {
                //year/month/date/dates/ week/datetime/datetimerange/daterange
                dateType:['year','month','date','dates','week','datetime','datetimerange','daterange'],
                val:this.value 
            }
        },
        mounted(){
            let {defaultValue,type} = this.deploy
            if(typeof defaultValue !== 'undefined' && defaultValue!==null && defaultValue!==''){
                if(this.isContains(this.dateType,type)){
                    var reg=/^[+,-]{0,1}[1-9]+\d*$/
                    if(reg.test(defaultValue)){
                        let num = parseInt(defaultValue) 
                    }else if('now' === defaultValue.toLowerCase()){
                        
                    }
                }else{
                    this.val = defaultValue
                }
            }
        },
        watch:{
            val(val){
                this.$emit('input',val)
            },
            value(val){
                this.val = val
            }
        },
        methods:{
            getOptions(obj){
                let {options,prop,id} = obj
                if(Array.isArray(options)){
                    return options
                }
                if(typeof options === 'function'){
                    return options()||[]
                }
                let key = prop
                if(Object.prototype.toString.call(options) === '[object String]'){
                    key = options
                }else if(typeof prop === 'undefined'){
                    key = id
                }
                if(typeof(this.options) === 'object' &&
                         Object.prototype.toString.call(this.options).toLowerCase() == '[object object]' && 
                         !this.options.length){
                    if(Array.isArray(this.options[key])){
                        return this.options[key]
                    }
                    if(Array.isArray(this.options[prop])){
                        return this.options[prop]
                    }
                }
                return []
            },
            isSelected(obj){
                let {options} = obj
                if(typeof options!=='undefined'){
                    return true
                }
                return false
            },
            isContains(obj,val){
                if(Object.prototype.toString.call(val) !== '[object String]'){
                    return false
                }
                let v = val.replace(/(^\s*)|(\s*$)/g,'').toLowerCase()
                if(Array.isArray(obj)){
                    for(let i in obj){
                        let o = obj[i]
                        if(Object.prototype.toString.call(o) !== '[object String]'){
                            continue
                        }
                        if(v === o.toLowerCase()){
                            return true
                        }
                    }
                }else if(Object.prototype.toString.call(obj) === '[object String]'){
                    return v === obj.toLowerCase()
                }
                return false
            },
            getDatePickerBind(row){
                return Object.assign({
                    'start-placeholder':'开始时间',
                    'end-placeholder':'结束时间',
                    'placeholder':'请选择时间',
                    format:'yyyy-MM-dd',
                    'default-value':new Date(),
                    'default-time':['00:00:00','23:59:59'],
                    'value-format':'yyyy-MM-dd HH:mm:ss',
                    size:'small'
                },row,{'listeners':null,rules:null})
            },
            getRadioBind(row){
                return Object.assign({

                },row,{'listeners':null,rules:null})
            },
            getSelectedBind(row){
                return Object.assign({
                    placeholder:'请选择',
                    size:'small',
                    'value-key':'value',

                },row,{'listeners':null,rules:null})
            },
            getBind(row){
                return Object.assign({
                    placeholder:'请输入值',
                    size:'small'
                },row,{'listeners':null,rules:null})
            },
            getListeners(obj){
               let result = Object.assign({},this.$listeners)
                if(typeof(obj) === 'object' && Object.prototype.toString.call(obj).toLowerCase() == '[object object]' && !obj.length){
                    let {listeners} = obj
                    Object.assign(result,listeners)
                }
                return result
            }
        }
    }
</script>
<style lang="less" scoped>
   .data-inline{
    -webkit-appearance: none;
    background-color: #fff;
    background-image: none;
    border-radius: 4px;
    border: 1px solid #dcdfe6;
    -webkit-box-sizing: border-box;
    box-sizing: border-box;
    color: #606266;
    display: inline-block;
    font-size: inherit;
    outline: 0;
    padding: 0 5px;
    -webkit-transition: border-color .2s cubic-bezier(.645,.045,.355,1);
    transition: border-color .2s cubic-bezier(.645,.045,.355,1);
    width: 100%;
   }
   .time_show_span{
    color: #c0c6d1;
   }
</style>