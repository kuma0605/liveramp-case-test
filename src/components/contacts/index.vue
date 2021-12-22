<template>
    <div class="ct_outer">
    <div class="ct_contacts">
        <div class="search_input">
            <img class="ct_icon" src="@/assets/search.png" alt="">
            <input type="text" placeholder="Search..." v-model="searchStr">
        </div>
        <ul class="ct_list">
            <template v-for="(letter,index) in alphabet" >
                <li    :key="index">
                    <div class="ct_category">
                        {{letter}}
                    </div>
                    <ul class="ct_content">
                        <li v-for="(item, innerIndex) in showData[letter]" :key="innerIndex">
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
                <li v-for="(letter,index) in alphabet" :key="index">
                    {{letter}}
                </li>
            </ul>
            <img class="icon_ct" src="@/assets/down.png" alt="">
        </div>
    </div>
</template>
<script>
    const alphabet = ["A","B","C","D","E","F","G","H","I","J","K","L","M","N","O","P","Q","R","S","T","U","V","W","X","Y","Z"];
    export default {
        name:"contacts",
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
                alphabet,
                searchStr:""
            }
        },
        computed:{
            showData(){
                let res = {};
                alphabet.forEach((letter)=>{
                    let list = this.mapData[letter];
                    if(list){
                        let listStr = JSON.stringify(list);
                        if(listStr.includes(this.searchStr)){
                            res[letter]=list.filter((item)=>{
                                return item.name.includes(this.searchStr)
                            })
                        }
                    }
                    
                })
                return res;
            }
        }
    }
</script>
<style lang="scss" scoped src="./index.scss"></style>