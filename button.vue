<template>
    <div>
        <el-button @click="sendPhone" :disabled="time<60">{{time==60?"发送验证码":`倒计时${time}S`}}</el-button>
    </div>
</template>
<script>
import {getSession,getCode} from '../http/request'
export default {
    data(){
        return{
            time:60,
            timer:null,
            sessionKey:'',
            sessionSecret:''
        }
    },
    props:["phone"],
    methods:{
        getCode(){
            var self=this;
            self.time--;
            this.timer=window.setInterval(function(){
                self.time--;
                if(self.time<0){
                    window.clearInterval(self.timer);
                    self.time=60;
                }
            },1000)
        },
        sendPhone(){

            getCode({mobile:this.phone,sessionKey:this.sessionKey,sessionSecret:this.sessionSecret}).then(res=>{
                res.code&&this.$message.error(res.data);
                !res.code&&(()=>{
                    this.getCode();
                })();

            })

        },
        getSession(){
            getSession({}).then(res=>{
                this.sessionKey=res.data.sessionKey
                this.sessionSecret=res.data.sessionSecret
            })
        },

    },
    created(){
        this.getSession()
    }
}
</script>
