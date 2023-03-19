<template>
  <Tree 
  :data.sync="data"
  :fileDrop="fileDrop"
  :directorDrop="directorDrop"
  :delete="del"
  ></Tree>
</template>

<script>
import Tree from '@/components/Tree/Tree'
export default {
    data(){
        return{
            data: [{
                label: '一级 1',
                type:'parent',
                id:1,
                children: [{
                    id:2,
                    label: '二级 1-1',
                    type:'parent',
                    children: [{
                        id:3,
                    label: '三级 1-1-1'
                    }]
                }]
                }, {
                label: '一级 2',
                type:'parent',
                id:4,
                children: [{
                    label: '二级 2-1',
                    type:'parent',
                    id:5,
                    children: [{
                    label: '三级 2-1-1',
                    id:6,
                    }]
                }, {
                    label: '二级 2-2',
                    type:'parent',
                    id:7,
                    children: [{
                    label: '三级 2-2-1',
                    id:8,
                    }]
                }]
                }, {
                label: '一级 3',
                type:'parent',
                id:9,
                children: [{
                    label: '二级 3-1',
                    type:'parent',
                    id:10,
                    children: [{
                    label: '三级 3-1-1',
                    id:11,
                    }]
                }, {
                    label: '二级 3-2',
                    type:'parent',
                    id:12,
                    children: [{
                    label: '三级 3-2-1',
                    id:13,
                    }]
                }]
        }],
            fileDrop:[
                {type:'rm',value:'删除文件'},
                {type:'rn',value:'修改名字'},
                {type:'new',value:'新建文件'},
            ],
            directorDrop:[
                {type:'rm',value:'删除文件夹'},
                {type:'rn',value:'修改名字'},
                {type:'new',value:'新建文件'}
            ]
        }
    },
    components:{
        Tree
    },
    methods:{
        del(){
            return new Promise((resolve,reject)=>{
                setTimeout(()=>{
                    resolve()
                },1000)
            })
        }
    }

}
</script>

<style>

</style>