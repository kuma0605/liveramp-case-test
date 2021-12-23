<template>
    <div class="ct_outer">
        <div class="ct_contacts" ref="contactContainer" >
            <div class="search_input">
                <img class="ct_icon" src="@/assets/search.png" alt="">
                <input type="text" placeholder="Search..." v-model="searchQuery" > 
            </div>
            <ul class="ct_list" ref="contactList">
                <template v-for="(letter,index) in validAlphabet" >
                    <li class="parentLi" :key="index" >
                        <div class="ct_category">
                            {{letter}}
                        </div>
                        <ul class="ct_content">
                            <li v-for="(item, innerIndex) in showData[letter]" :key="innerIndex" @click="doChoose(letter,innerIndex,item)">
                                {{item.name}}
                            </li>
                        </ul>
                    </li>
                </template>
            </ul>
        </div>
        <div class="right_bar">
            <img class="icon_ct" src="@/assets/up.png" alt="">
            <ul class="letter_bar">
                <li v-for="(letter,index) in validAlphabet" :key="index" @click="doJump(index)">
                    {{letter}}
                </li>
            </ul>
            <img class="icon_ct" src="@/assets/down.png" alt="">
        </div>
        <card :chosen="chosen" v-if="chosen" @close="doClose" @doup="doup" @dodown="dodown"></card>
    </div>
</template>
<script>
    import card from "../card"
    var _ = require("lodash")
    const alphabet = ["A","B","C","D","E","F","G","H","I","J","K","L","M","N","O","P","Q","R","S","T","U","V","W","X","Y","Z"];
    export default {
        name:"contacts",
        components:{
            card
        },
        props:{
            mapData:{
                type:Object,
                default(){
                    return {}
                }
            }
        },
        data(){
            return {
                searchQuery:"",
                showData:{},
                validAlphabet:[],
                chosen:undefined,
                letterChosen:undefined,
                indexChosen:undefined
            }
        },
        watch: {
            searchQuery: function () {
                this.expensiveOperation()
            }
        },
        created(){
            this.handleSearch()
        },
        methods:{
            doJump(index){
                let liItems = this.$refs.contactList.querySelectorAll(".parentLi");
                let liItem = liItems[index]
                let offsetTop = liItem.offsetTop;
                let ctOffsetTop = this.$refs.contactContainer.offsetTop;
                
                this.$refs.contactContainer.scrollTop=offsetTop-ctOffsetTop;
            },
            // debounce
            expensiveOperation: _.debounce(function () {
                
                //记录查询
                this.recordSearch()
                //处理查询
                this.handleSearch()
            }, 500),
            recordSearch(){

            },
            handleSearch(){
                let res = {};
                let validAlphabet=[]
                alphabet.forEach((letter)=>{
                    let list = this.mapData[letter];
                    if(list&&list.length){
                        let listStr = JSON.stringify(list).toLowerCase();
                        let searchQuery = this.searchQuery.toLowerCase();
                        //查找对应 内容字符串是否包含 搜索内容
                        if(listStr.indexOf(searchQuery)!=-1){
                            
                            let matchList=list.filter((item)=>{
                                //名字包含搜索内容
                                let name = item.name.toLowerCase();
                                return name.indexOf(this.searchQuery)!=-1
                            })

                            if(matchList.length){
                                validAlphabet.push(letter)
                                res[letter]=matchList
                            }
                        }
                    }
                })
                this.showData=res;
                this.validAlphabet=validAlphabet;
            },
            doChoose(letter,index,item){
                this.letterChosen = letter;
                this.indexChosen = index;
                this.chosen=item;
            },
            doClose(){
                this.letterChosen = undefined;
                this.indexChosen = undefined;
                this.chosen=undefined;
            },
            doup(){
                if(this.indexChosen===0){
                    
                }else{
                    let indexChosen=this.indexChosen-1
                    this.indexChosen = indexChosen;
                    this.chosen= this.showData[this.letterChosen][indexChosen]
                }
            },
            dodown(){
                let lastIndex = this.showData[this.letterChosen].length-1
                if(this.indexChosen===lastIndex){
                    
                }else{
                    let indexChosen=this.indexChosen+1
                    this.indexChosen = indexChosen;
                    this.chosen= this.showData[this.letterChosen][indexChosen]
                }
            }
        }
    }
</script>
<style lang="scss" scoped src="./index.scss"></style>