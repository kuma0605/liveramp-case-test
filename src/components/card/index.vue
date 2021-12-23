<template>
    <div class="ct_card" @click.self="clickEvent">
        <div class="wrap_outer">
            <img class="icon_ct" src="@/assets/up.png" alt="" @click="doup">
            <div class="wrap_card">
                <div class="content_1">
                    <img class="avatar_ct" src="@/assets/avatar.png" alt="">
                    <div class="other_ct">
                        <div class="name_ct">
                            {{chosen.name}}
                        </div>
                        <div class="extra_ct">
                            <div>
                               0 followers
                            </div>
                            <div>
                               0 starts
                            </div>
                        </div>
                    </div>
                </div>
                <div class="content_ct">
                    Email:
                </div>
                <div class="content_ct">
                    Bio:
                </div>
            </div>
            <img class="icon_ct" src="@/assets/down.png" alt="" @click="dodown">
        </div>
        
    </div>
</template>
<script>
    export default {
        props:{
            chosen:{
                type:Object,
                default(){
                    return {}
                }
            }
        },
        data(){
            return {

            }
        },
        methods:{
            clickEvent(){
                this.$emit("close")
            },
            doup(){
                this.$emit("doup")
            },
            dodown(){
                this.$emit("dodown")
            }
        }
    }
</script>
<style lang="scss" scoped src="./index.scss"></style>