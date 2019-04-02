<style lang="less" scoped>
@css-prefix:'tc-';
@tree-pre-cls:~'@{css-prefix}tree-';
        .@{tree-pre-cls}tree_ul{
            height: 200px;
            vertical-align: top;
            overflow-Y: auto;;
            display: inline-block;
            list-style: none;
            padding: 0;
            margin: 0;
            border: 1px solid #dcdfe6;
            box-sizing: border-box;
            .clickId{
                // background-color: #c4c5ca;
                color: pink;
            }
            .@{tree-pre-cls}tree_li{
                    width: 120px;
                    font-size: 14px;
                    white-space: nowrap;
                    text-overflow: ellipsis;
                    color: #606266;
                    height: 34px;
                    line-height: 1.5;
                    box-sizing: border-box;
                    cursor: pointer;
                    outline: none;
                .@{tree-pre-cls}tree_label{
                    display: inline-block;
                    box-sizing: border-box;
                    display: inline-block;
                    width: 100%;
                    height: 34px;
                    line-height: 34px;
                    text-align: center;
                }
               
            }
        }
        .tree_chi{
            
        }
</style>

<template>
    <span  >
        <ul :class="pleCls+'tree_ul'" v-if="list && list.length">
            <li :class="pleCls+'tree_li'" v-for="(item,index) in list" :key="index"  >
                <span 
                    @click="clickItem(item,count)"  
                    :class="[item.selected ? 'clickId' : '',pleCls+'tree_label']" 
                >{{item[defaultProps.label]}}</span>
            </li>
        </ul><tree-menu  
            :defaultValue='defaultValue'
            v-if="sublist && sublist.length "
            :list="sublist" 
            :count="count+1" 
            @endSel='endSel'
            @recursive='recursive' >
        </tree-menu>
    </span>
</template>

<script>
const pleCls = 'tc-tree-'
    export default {
            name:'treeMenu',
            props:{
                defaultValue:{
                    type:Array,
                    default:()=>{
                        return []
                    }
                },
                count:{
                   type:Number,
                   default:0
                },
                list:{
                    type:Array,
                    default:[]
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
                // isEnd:{
                //     type:Boolean,
                //     default:true
                // }
            },
            computed:{
               
            },
            data(){
                return{
                    pleCls:pleCls,
                    cheData:[],
                    sublist:[],//点击 有子集的集合
                    selected:false,
                }
            },
            methods:{
                // 处理数据设置未选中
                removeData(){
                    let selt = this
                        this.list.forEach(node => {
                         selt.$set(node,'selected',false) 
                         this.sublist=[]
                    });
                },
                endSel(val){
                     this.$emit('endSel',false)
                },
                // 如果有默认值 聚焦高亮
                setSelect(BIg,smart){
                    BIg.forEach((fa)=>{
                            if(fa[this.defaultProps.value] == smart[this.count][this.defaultProps.value]){
                                this.clickItem(fa,this.count,0)
                            }
                    })

                },
                //点击选中高亮
                clickItem(item,count,index){
                    // debugger
                    let selt = this
                    if(!item[selt.defaultProps.children]){
                        this.$emit('endSel',false)
                    }
                    function findCompant(context){
                       if(context.$children && context.$children.length){
                            context.$children.forEach(item=>{
                                item.sublist =[]
                                if(item.$children && item.$children.length){
                                   findCompant(item)
                                }
                           })
                       }
                    }
                    findCompant(this)
                    function deep(node){
                         selt.$set(node,'selected',false) 
                         if(node[selt.defaultProps.children] && node[selt.defaultProps.children].length){
                             selt.sublist = item[selt.defaultProps.children]
                             node[selt.defaultProps.children].forEach(child=>{
                                 deep(child)
                             })
                         }
                    }
                    this.list.forEach(node => {
                        deep(node)
                         selt.$set(item,'selected',true) 
                    });
                    this.$emit('recursive',[item,count]) 
                    

                }, 
                recursive(item){//向父级传递数据
                    this.cheData[item[1]]=this.getObj(item[0],this.defaultProps.value,this.defaultProps.label)
                    this.$emit('recursive',item)
                },
                // 获取需要的数据
                getObj(obj,value,label){
                    var newObj={}
                    newObj.value= obj.value 
                    newObj.label= obj.label 
                    return newObj
                },
                
            },
            created(){
                if(this.defaultValue.length){
                    this.setSelect(this.list,this.defaultValue)
                }

            },
            mounted(){
            }
    }
</script>

