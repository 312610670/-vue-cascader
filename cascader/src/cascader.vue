<style lang="less" scoped>

@css-prefix:'tc-';
@cascader-pre-cls: ~'@{css-prefix}cascader-';
    .@{cascader-pre-cls}cas_box{
         
            .@{cascader-pre-cls}input--suffix{
                        display: block;
                        position: relative;
                        font-size: 14px;
                        width: 100%;
                        .@{cascader-pre-cls}input__inner{
                            padding-right: 30px;
                            background-color: #fff;
                            background-image: none;
                            border-radius: 4px;
                            border: 1px solid #dcdfe6;
                            box-sizing: border-box;
                            color: #606266;
                            display: inline-block;
                            font-size: inherit;
                            outline: none;
                            padding: 0 15px;
                            transition: border-color .2s cubic-bezier(.645,.045,.355,1);
                            width: 100%;
                        }
                        .@{cascader-pre-cls}input__suffix{
                                position: absolute;
                                height: 100%;
                                right: 5px;
                                top: 0;
                                text-align: center;
                                color: #c0c4cc;
                                transition: all .3s;
                                pointer-events: none;
                            .@{cascader-pre-cls}input__suffix-inner{
                                    pointer-events: all;
                                    .select__caret{
                                        color: #c0c4cc;
                                        font-size: 14px;
                                        transition: transform .3s;
                                        transform: rotate(180deg);
                                        cursor: pointer;
                                    }
                                    .@{cascader-pre-cls}input__icon{
                                        margin-top: 10px;
                                        display: inline-block;
                                        width: 25px;
                                       
                                        height: 19px;
                                        text-align: center;
                                        transition: all .3s;
                                        background-color: #fff;
                                        background:url('../img/iconfont-platform.svg') no-repeat;
                                        background-size: 25px 19px
                                    } 
                                 
                            }
                        }
                       .@{cascader-pre-cls}input-cle{
                            background: url('../img/del.svg') no-repeat;
                            background-size: 25px 19px;
                            position: absolute;
                            top: 10px;
                            right: -25px;
                            display: inline-block;
                            width: 25px;
                            height: 19px;
                            text-align: center;
                        }
            }
            .@{cascader-pre-cls}cas_select{

            }
    }
</style>

<template>
    <div :class ="prlCls+'cas_box'" ref="box" :style="`width:${width}px;height:${height}px;`">
            {{result}}
        <div :class=" prlCls+'input--suffix'" @click="showList=!showList">
            <input type="text" 
                :style="`height: ${height}px;line-height: ${height}px;font-size:${fontSize}px`"
                :readonly="!filterable"
                autocomplete="off" 
                placeholder="请选择" 
                :class="prlCls+'input__inner'" 
                v-model="searchName">
            <span :class="prlCls+'input__suffix'">
                <span :class="prlCls+'input__suffix-inner'"  ><i  :style=" `line-height: ${height}px;`" :class="[prlCls+'input__icon',showList ? 'select__caret' :'']" ></i> </span>
            </span>
            <span  v-show="searchName" :class="prlCls+'input-cle'" @click.stop="RemoveData"></span>
        </div>
        <div  :class="prlCls+'cas_select'" v-show="showList" style="width:600px;" >
                <tree 
                    :defaultValue='value'
                    :list='options'
                    :defaultProps="defaultProps"
                    @recursive='recursive'
                    @endSel='endSel'
                    ref="treemenu"
                ></tree>
        </div>
    </div>
</template>

<script>
/**
 *  <cascader
        v-model="endArr"
        :options="options"
        :defaultProps="defaultProps"
    ></cascader>
 * cascader
 * @param endArr 绑定的值 可以设置默认值
 * @param options 传入的多维数组 [Array]
 * @param defaultProps 绑定值 [Object] 定义传进去数组的 key 值
 *  defaultProps{
 *      children: 'children', //数组的子数组
        label: 'label', //展示的值
        value: 'value'  //绑定的ID

 *  }
 * 
 * 
 * @emit on-change 值改变
 */

import tree from './tree.vue'
const prlCls = 'tc-cascader-'
     export default {
        data(){
            return {
                prlCls:prlCls,
                showList:'',
                result: [],
                oldCount:0,
                width:this.defaultSize.width,
                height:this.defaultSize.height,
                fontSize:this.defaultSize.fontSize,
            }
        },
        props:{
            value:{
                type:Array,
                default:[]
            },
            options:{
                type:Array,
                default:''
            },
            filterable:{
                type:Boolean,
                default:false
            },
            defaultProps:{
                type:Object,
                default:()=>{
                    return{
                        children: 'children',
                        label: 'label',
                        value: 'value'
                    }
                }
            },
            defaultSize:{
                type:Object,
                default:()=>{
                    return {
                        width: 200,
                        height: 40,
                        fontSize:14
                    }
                }
            }
           
        },
        components:{
            tree
        },
        computed:{
            searchName(){
                let show=''
                this.result.forEach((element,index) => {
                  show+=this.getShow(element)+'/'
                }); 
                if(!this.showList){
                    show =  show.slice(0,show.length-1)
                }
                return show
            },
        },
 
        methods:{
            // 清空数据
            RemoveData(){
                this.result=[]
                this.$emit('input',[])
                this.$refs.treemenu.removeData()
            },
            endSel(val){
                this.showList = val
                this.$emit('input',this.result) //返回的对象
            },
            // 递归遍历数组选中需要的值
            recursive(arr){
                if(arr[1]==0){
                    this.result=[]
                    
                }else{
                    if(arr[1] < this.oldCount){
                        this.result=this.result.slice(0,arr[1]+1)
                    }else{
                    }
                }
              
                this.oldCount = arr[1]
                this.$set(this.result,arr[1],this.getObj(arr[0],this.defaultProps.value,this.defaultProps.label))
            },
             getObj(obj,value,label){
                var newObj={}
                newObj.value= obj.value 
                newObj.label= obj.label 
                return newObj
            },
            getShow(item){
                var str = ''
                Object.keys(item).forEach(i=>{
                    if(i == this.defaultProps.label){
                        str =  item[i]
                    }
                })
                return str
            },
        },
        created(){
        },
        mounted(){
            document.addEventListener('click',(e)=>{
                if(this.$refs.box){
                    if(!this.$refs.box.contains(e.target)){
                        this.showList = false;
                    }
                }
            })
        }
    }
</script>

<style scoped>

</style>