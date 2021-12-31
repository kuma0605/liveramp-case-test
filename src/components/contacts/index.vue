<template>
    <div class="ct_outer" @click.self="selfEvent">
        <div class="ct_contacts" ref="contactContainer" >
            <div class="search_input" >
                <div class="input_container">
                    <img class="ct_icon" src="@/assets/search.png" alt="">
                    <input type="text" v-model.trim="searchQuery"  @focus="focusEvent" >
                    <div class="ct_btn" @click="doQuery">search</div>
                </div>
                <div class="history_ct" v-show="showHistory&&validHistory.length">
                    <ul >
                        <li v-for="(item,index) in validHistory" :key="index" @click="clickEvent(item)">
                            {{item}}
                        </li>
                    </ul>
                    <div class="hideHistory" @click="showHistory=false">hide</div>
                </div>
            </div>
            <!-- <search-input v-model.trim="searchQuery" :history-list="historyList" :show-list="showList" @chooseHistory="chooseEvent"></search-input> -->
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
    const validTime = 24*60*60*1000;
    import card from "../card"
    // import searchInput from "../searchInput"
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
                indexChosen:undefined,
                historyList:[],
                showHistory:false
            }
        },
        /* watch: {
            searchQuery: function () {
                this.showHistory=true;
                this.expensiveOperation()
            }
        }, */
        computed:{
            validHistory(){
                return this.historyList.filter((name)=>{
                    //优化匹配，不区分大小写
                    name = name.toLowerCase();
                    let searchQuery = this.searchQuery.toLowerCase();
                    return name.indexOf(searchQuery)!=-1
                })
            }
        },
        created(){
            this.handleSearch();
            this.getHistory()
        },
        methods:{
            doJump(index){
                // offsetTop 是当前元素 距离 上级定位元素的距离，不会随着滚动而改变。
                // 给 $refs.contactContainer 加上 定位，使其变为定位元素，优化代码
                let liItems = this.$refs.contactList.querySelectorAll(".parentLi");
                let liItem = liItems[index]
                let offsetTop = liItem.offsetTop;
                // let ctOffsetTop = this.$refs.contactContainer.offsetTop;

                // console.log(offsetTop, ctOffsetTop, liItem.offsetParent)
                
                this.$refs.contactContainer.scrollTop=offsetTop;
            },
            // debounce, unused now
            expensiveOperation: _.debounce(function () {
                this.showHistory=false;
                //记录查询
                this.recordSearch()
                //处理查询
                this.handleSearch()
            }, 1000),
            recordSearch(){
                if(this.searchQuery){
                    let searchQuery = this.searchQuery;
                    let timeStamp = new Date().getTime()
                    let historyQuery = localStorage.getItem("searchQuery");
                    let queryArr = [];
                    let validQueryArr = [];
                    let validHistory=[];
                    if(historyQuery){
                        //这段代码可再优化
                        //使用正则表达式剔除最新查找的过往查询
                        //再进行时间筛选的优化，从头开始匹配，若匹配到时间在有效期内，则停止匹配，将该有效时间之前的数据，利用正则剔除
                        //这样再加上最新的查询
                        //得到新的查询历史，存入localStorage
                        queryArr=historyQuery.split(",");
                        let nowtime = new Date().getTime();
                        queryArr.forEach((query)=>{
                            let arr = query.split("&time=");
                            let timeStamp = arr[1]-0;
                            if((nowtime-timeStamp<validTime)&&(this.searchQuery!=arr[0])){
                                validQueryArr.push(query)
                                validHistory.push(arr[0])
                            }
                        })

                        // new way
                        let pattern = this.searchQuery+"&time="+'\d+,';
                        let handledHistory = historyQuery.replace(new RegExp(pattern,'gi'))
                        
                        

                    }
                    validHistory.push(searchQuery);
                    this.historyList=validHistory.reverse();
                    
                    validQueryArr.push(searchQuery+"&time="+timeStamp);
                    localStorage.setItem("searchQuery", validQueryArr.join(","))
                }
                
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
                                return name.indexOf(searchQuery)!=-1
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
                    let lastLetterIndex = this.validAlphabet.indexOf(this.letterChosen)-1
                    if(lastLetterIndex!=-1){
                        let letterChosen = this.validAlphabet[lastLetterIndex];
                        let indexChosen = this.showData[letterChosen].length-1;
                        let chosen = this.showData[letterChosen][indexChosen];
                        this.indexChosen=indexChosen;
                        this.letterChosen = letterChosen;
                        this.chosen = chosen;
                    }
                }else{
                    let indexChosen=this.indexChosen-1
                    this.indexChosen = indexChosen;
                    this.chosen= this.showData[this.letterChosen][indexChosen]
                }
            },
            dodown(){
                let lastIndex = this.showData[this.letterChosen].length-1
                if(this.indexChosen===lastIndex){
                    let nextLetterIndex = this.validAlphabet.indexOf(this.letterChosen) + 1;
                    if(nextLetterIndex!=this.validAlphabet.length){
                        let letterChosen = this.validAlphabet[nextLetterIndex];
                        let indexChosen = this.showData[letterChosen].length-1;
                        let chosen = this.showData[letterChosen][indexChosen];
                        this.indexChosen=indexChosen;
                        this.letterChosen = letterChosen;
                        this.chosen = chosen;
                    }
                }else{
                    let indexChosen=this.indexChosen+1
                    this.indexChosen = indexChosen;
                    this.chosen= this.showData[this.letterChosen][indexChosen]
                }
            },
            
            getHistory(){
                let historyQuery = localStorage.getItem("searchQuery");
                if(!historyQuery){ //null
                    return;
                }
                let queryArr=historyQuery.split(",");
                let validHistory=[];
                let validQueryArr=[];
                let nowtime = new Date().getTime();
                //筛选有效时间的代码可优化
                queryArr.forEach((query)=>{
                    let arr = query.split("&time=");
                    let timeStamp = arr[1]-0;
                    if(nowtime-timeStamp<validTime){
                        validHistory.push(arr[0])
                        validQueryArr.push(query)
                    }
                })
                this.historyList=validHistory.reverse();
                if(validQueryArr.length){
                    localStorage.setItem("searchQuery", validQueryArr.join(","))
                }else{
                    localStorage.removeItem("searchQuery")
                }
                
            },
            /* chooseEvent(queryStr){
                this.searchQuery= queryStr;
            }, */
            focusEvent(){
                this.getHistory();
                this.showHistory=true;
            },
            
            clickEvent(queryStr){
                this.showHistory=false;
                this.searchQuery=queryStr;
                
            },
            doQuery(){
                

                this.showHistory=false;
                //记录查询
                this.recordSearch()
                //处理查询
                this.handleSearch()
            },
            selfEvent(){
                this.showHistory=false;
            }
        }
    }
</script>
<style lang="scss" scoped src="./index.scss"></style>