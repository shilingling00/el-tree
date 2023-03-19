<template>
  <div>
      <el-tree 
      class="filter-tree"
      :data="allData" 
      default-expand-all
      :expand-on-click-node="false"
      :props="defaultProps" 
      @node-click="handleNodeClick"
      :render-content="renderContent"
      ref="tree"
      ></el-tree>
  </div>
  
</template>

<script>
import _ from 'lodash'
export default {
    props:{
      data:{
        type:Array,
        default:()=>[]
      },
      
      fileDrop:Array,
      directorDrop:Array,
      delete:Function
    },
    data() {
      return {
        allData:[],
        filterText: '',
        list:[],
        defaultProps: {
          children: 'children',
          label: 'label'
        },
        currentId:'',
        currendContent:''
      };
    },
    methods: {
      transformData(){
                //克隆数据，防止在子组件中操作父组件数据
                let AllData=_.cloneDeep(this.data);
                this.allData=AllData;
      },
      isParent(data){
          // return data.type=='parent' 
          return data.children?true:false
      },
      handleName(data){//修改名字
          this.currendContent=data.label;
          this.currentId=data.id;
      },
      remove(id){
        let list=_.cloneDeep(this.data);
        // list=list.filter(item=>item.id!=id);
        this.spliceList(list,id)
        this.$emit('update:data',list)
        this.$message({
            type: 'success',
            message: '删除成功!'
          });
      },
      spliceList(list,id){
        for(var i=0;i<list.length;i++){
          if(list[i].id==id){
            list.splice(i,1);
            break;
          }
          if(list[i].children){
            this.spliceList(list[i].children,id)
          }   
        }
        return list
      },
      handleDelete(data){//删除文件
        this.$confirm(`此操作将永久删除${data.label}, 是否继续?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          //不能直接将数据删除，需要调用用户的方法删除
          //如果用户传递了delete方法，可以直接调用
          this.delete?this.delete(data.id).then(()=>{
            this.remove(data.id)
          }):this.remove(data.id)
          
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });          
        });
      },
      forNewOne(list,id,{value}){
        for(var i=0;i<list.length;i++){
           if(list[i].id==id){
            if(list[i].children){
              list[i].children.push({id:new Date().getTime(),label:value })
            }else{
              list[i].type='parent';
              list[i].children=[{id:new Date().getTime(),label:value}]
            }
            break;
           }
           if(list[i].children){
            this.forNewOne(list[i].children,id,{value});    
           }
         }
         return list
      },
      newOne(id,{value}){
        let list=_.cloneDeep(this.data);
        this.forNewOne(list,id,{value})
         this.$emit('update:data',list);
         this.$message({
            type: 'success',
            message: '新文件是: ' + value
          });
      },
      creatNew(data){//新建    
        this.$prompt('请输入文件名字', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
        }).then(({ value }) => {
          this.newOne(data.id,{value})
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '取消输入'
          });       
        });
      },
      
      handleCommand(data,value){
          //bind绑定时，会把原来的参数往后移动
          if(value=='rn'){
            this.handleName(data)
          }else if(value=='rm'){
            this.handleDelete(data)
          }else if(value=='new'){
            this.creatNew(data)
          }
      },
      ok(data){//确定按钮
        let list=_.cloneDeep(this.data);
        // let items=list.find(item=>item.id==data.id);
        // items.label=this.currendContent;
        this.reName(list,data)
        this.currentId='';
        this.$emit('update:data',list);
        this.$message({
            type: 'success',
            message: '修改成功!'
          });
      },
      reName(list,data){
          for(var i=0;i<list.length;i++){
                if(list[i].id==data.id){
                  list[i].label=this.currendContent;
                  break;
                }
                if(list[i].children){
                  this.reName(list[i].children,data)
                }
          }
          return list
      },
      cancel(){//取消按钮
          this.currentId=''
      },
      handleInput(v){
          this.currendContent=v
      },
      renderContent(h, { node, data, store }){  
          var list=this.isParent(data)?this.directorDrop:this.fileDrop;
          return (
          <div  style={{width:'100%'}}>
            {
                this.isParent(data)?
                node.expanded?
                <i  class="el-icon-folder-opened"></i>:
                <i  class="el-icon-folder"></i>:
                <i  class="el-icon-document"></i>
            }

            {
              this.currentId==data.id?<el-input value={this.currendContent}  on-input={this.handleInput}></el-input>:data.label
            }

            {
              this.currentId!==data.id?<el-dropdown  placement="bottom-start"  trigger="click"  
              on-command={this.handleCommand.bind(this,data)}>
              <span class="el-dropdown-link">
                <i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu slot="dropdown">
                {
                  list.map((item)=>{
                    return <el-dropdown-item command={item.type} >{item.value}</el-dropdown-item>
                  })
                }
              </el-dropdown-menu>
            </el-dropdown>:<span style={{float:'right'}}>
              <el-button type="text"  on-click={this.ok.bind(this,data)}>确认</el-button>
              <el-button type="text"  on-click={this.cancel.bind(this,data)}>取消</el-button>
            </span>
            }



            

            
          </div>);
      },
      handleNodeClick(data) {
        
      }
    },
    mounted(){
      this.transformData()
    },
    watch:{
      data(){
        this.transformData()
      }
    }
}
</script>

<style>
.el-tree{
  margin-top: 30px;
  width: 400px;
}
.el-tree-node__content{
  height: 40px;
  
}
.el-dropdown{
  float: right;
}
.el-input{
  width: 70%;
}
</style>