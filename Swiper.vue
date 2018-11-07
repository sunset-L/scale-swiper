<!-- 接收宽度,高度,单位的传值,其中如果单位不是px是vw需要传屏幕宽度,轮播图数量要改 -->
<template>
    <div
        class="com-root"
        :style="rootStyle"
        @touchstart="conTouchstart"
        @touchmove="conTouchmove"
        @touchend="conTouchend"
    >
        <div id="container" :style="containerStyle" @transitionend="conTranEnd">
            <!-- 多创建后面两张,要改 -->
            <div
                class="sw-item"
                :style="[{
                backgroundImage:'url(/img/3.jpg)',
                transform:-1==currentIndex-1?'scale(1,1)':'',
                },
                swItemStyle
                ]"
            ><p>-2</p></div>
            <div
                class="sw-item"
                :style="[{
                backgroundImage:'url(/img/4.jpg)',
                transform:0==currentIndex-1?'scale(1,1)':''
                },
                swItemStyle
                ]"
            ><p>-1</p></div>
            <!-- for创建实质内容,要改 -->
            <div
                class="sw-item"
                v-for="i in 4"
                :style="[{
                    backgroundImage:'url(/img/'+i+'.jpg)',
                    transform:i==currentIndex-1?'scale(1,1)':'',
                    opacity:i==currentIndex-1?'1':''
                    },swItemStyle
                ]"
            ><p>{{i}}</p></div>
            <!-- 多创建前面两张,要改 -->
            <div
                class="sw-item"
                :style="[{
                backgroundImage:'url(/img/1.jpg)',
                transform:5==currentIndex-1?'scale(1,1)':''
                },
                swItemStyle
                ]"
            ><p>+1</p></div>
            <div
                class="sw-item"
                :style="[{
                backgroundImage:'url(/img/2.jpg)',
                transform:6==currentIndex-1?'scale(1,1)':''},
                swItemStyle
                ]"
            ><p>+2</p></div>
        </div>
    </div>
</template>

<script>
    export default {
        name: "Swiper",
        props:{
            widthProp:{
                type:Number,
                default:600,
            },
            heightProp:{
                type:Number,
                default:400
            },
            unit:{
                type:String,
                default:"px"
            },
            clientWidth:{
                type:Number
            }
        },
        mounted(){

            if(this.unit=="px"){
                this.width = this.widthProp;
                this.height = this.height;
            }else if(this.unit == "vw"){
                this.width = this.widthProp/100*this.clientWidth;
                this.height = this.heightProp/100*this.clientWidth;
            }
        },
        data(){
            return{
                width:null,
                height:null,
                margin:0,
                // 轮播索引
                currentIndex:2,
                // 临时变量,用来记录按下到抬起move了多少
                tempDistance:0,
                // 记录点击点
                startX:null,
                // 记录当前是否在手动move
                isMove:false,
                // 记录是否正在左右轮播动画
                isAni:false,
                // 记录是否"开启"scale动画
                isScale:true
            }
        },
        computed:{
            rootStyle(){
                return {
                    width:this.width+"px",
                    height: this.height+"px"
                }
            },
            swItemStyle(){
                let result = {
                    width:this.width*0.8+"px",
                    margin:"0 "+ this.width*this.margin+"px"
                };
                if(this.isScale){
                    result.transition = "all 0.7s";
                }
                return result;
            },
            containerStyle(){
                let result={
                    left:-this.width*(1-(0.1-this.margin)*2)*this.currentIndex+"px",
                    paddingLeft:this.width*(0.1-this.margin)+"px"
                };
                if(!this.isMove){
                    result.transition = "all 0.7s";
                }
                return result;
            },
        },
        methods:{

            // 手指按下时
            conTouchstart(e){
                if(this.isAni){
                    return;
                }
                this.tempDistance = e.touches[0].pageX;
                this.startX = e.touches[0].pageX;
                this.index = this.currentIndex
            },
            // 按下移动时
            conTouchmove(e){
                if(this.isAni){
                    return;
                }
                this.isMove = true;
                // 要改
                if(this.currentIndex>=7){
                    this.currentIndex = 7
                }else if(this.currentIndex<=0){
                    this.currentIndex = 0;
                }

                let moveX = e.touches[0].pageX
                // 一次move的距离
                let distance = moveX-this.startX;
                if(distance<0){
                    this.currentIndex = -distance/this.width+this.currentIndex;
                }else if(distance>0) {
                    this.currentIndex = this.currentIndex-distance/this.width;
                }
                this.startX = moveX

            },
            // 点击结束时
            conTouchend(e){
                this.isMove = false;
                this.isAni = true;
                // 往左100像素
                if(e.changedTouches[0].pageX-this.tempDistance<-100){
                    // 往左100
                    this.currentIndex = Math.ceil(this.currentIndex)
                }else if(e.changedTouches[0].pageX-this.tempDistance>100){
                    // 往右100
                    this.currentIndex = Math.floor(this.currentIndex)
                }else {
                    // 没动
                    this.currentIndex = Math.floor(this.currentIndex);
                    this.isAni = false;
                }
            },
            // 动画结束时
            conTranEnd(){
                this.isAni = false;
                // 这个6实际是第数组.length+1,要改
                if(this.currentIndex==6){
                    this.isMove = true;
                    this.currentIndex = 2;
                    this.isScale = false;
                    setTimeout(()=>{
                        this.isMove = false;
                        this.isScale = true;
                    },710)
                }else if(this.currentIndex==1){
                    this.isMove = true;
                    // 要改
                    this.currentIndex = 5;
                    this.isScale = false;
                    setTimeout(()=>{
                        this.isScale = true;
                        this.isMove = false;
                    },710)
                }

            },
        },
    }
</script>

<style scoped>
    .com-root{
        display: inline-block;
        overflow: hidden;
        -webkit-overflow:hidden;
        -moz-overflow:hidden;
    }
    #container{
        height: 100%;
        display: flex;
        position: relative;
    }
    .sw-item{
        flex-shrink: 0;
        height: 100%;
        background-size: 100% 100%;
        color: red;
        transform: scale(0.9,0.9);
        opacity: 0.8;
    }
    /*.sw-item:nth-of-type(1){
        !* 这里是0.1(内容宽度0.8,所以左右0.1)*组件宽度 *!
        margin-left:40px !important;
    }*/
</style>