<template>
    <div class="search_input" >
        <img class="ct_icon" src="@/assets/search.png" alt="">
        <input type="text" ref="inputSearch" placeholder="Search..." v-bind:value="value" v-on:input="$emit('input', $event.target.value)" @focus="focusEvent" > 
        <ul class="history_ct" v-show="showHistory&&validHistory.length">
            <li v-for="(item,index) in validHistory" :key="index" @click="clickEvent(item)">
                {{item}}
            </li>
        </ul>
    </div>
</template>

<script>
export default {
    props: {
        
        value:String,
        showList:{
          type:Boolean,
          default:false  
        },
        historyList:{
            type:Array,
            default(){
                return []
            }
        }
    },
    data(){
        return {
            showHistory:false,
            
        }
    },
    computed:{
        validHistory(){
            return this.historyList.filter((queryStr)=>{
                return queryStr.indexOf(this.value)!=-1
            })
        }
    },
    methods:{
        focusEvent(){
            this.showHistory=true;
        },
        blurEvent(){
            this.showHistory=false;
        },
        clickEvent(queryStr){
            this.showHistory=false;
            console.log(queryStr)
            this.$emit("chooseHistory",queryStr)
        }
    }
}
</script>

<style lang="scss" scoped src="./index.scss"></style>