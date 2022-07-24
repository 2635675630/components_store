<template>
  <div class="swiper" id="swiper" @mouseover="btn_hover_show" @mouseleave="btn_hover_hidden" >
    <div class="content" :style="{ '--position':position}">
        <a :href="swiper_imageLink[index]" :style="{'--animation_time':animation_time}" v-for="(item,index) in image" :key="item.src"><img :src="require('@/assets/img/'+item.src)" alt=""></a>
    </div>
    <button  @click="turnleft" class="left_button" :style="{ '--font_size':font_size,'--left_offset':left_offset}">&lt;</button>
    <button  @click="turnright" class="right_button" :style="{  '--font_size':font_size,'--right_offset':right_offset}">&gt;</button>
    <ul class="inditor" :style="{'--li_width':(100/image.length)+'%'}">
        <li v-for="(item,index) in image" :key="index" @mouseover="switch_image(index)" :style="{'--indicator_color':inditor_show_color[index]}"></li>
    </ul>
  </div>
</template>

<script>
export default {
    props:{
        //图片连接
        image:{
            type:Array,
            require:true
        },
        //图片点击连接
        imageLink:{
            type:Array,
            default:function(){return []}
        },
        //图片动画切换速度
        animation_time:{
            type:String,
            default:'0.3s'
        },
         //按钮宽度
        btn_width:{
            type:String,
            default:"10%"
        },
        //按钮高度
        btn_height:{
            type:String,
            default:"15%"
        },
        //按钮文字大小
        font_size:{
            type:String,
            default:'20px'
        },
        //判断是否使用按钮动画
        btn_show:{
            type:Boolean,
            default:true
        },
        //指示器颜色
        indicator_color:{
            type:String,
            default:'rgba(0,0,0,0.4)'
        },
        //指示器经过颜色
        indicator_hover_color:{
            type:String,
            default:'rgba(0,0,0,0.6)'
        }
    },
    data(){
        return{
            distance:0,//图片偏移量,
            btn_disable:false,//图片切换节流阀
            left_offset:'-500%',//左按钮偏移量
            right_offset:'500%',//右按钮偏移量
            swiper_imageLink:[],//存储图片连接
            inditor_show_color:[]//指示器颜色

        }
    },
    computed:{
        //返回图片位置左偏移量
        position(){
            return this.distance+'%'
        }
    },
    methods:{
        //图片左移事件
        turnleft(){
                 this.distance==0?this.distance=-100*(this.image.length-1):this.distance+=100
        },
        //图片右移事件
        turnright(){
                this.distance==-100*(this.image.length-1)?this.distance=0:this.distance-=100
        },
        //显示按钮
        btn_hover_show(){
            this.left_offset='-350%'
            this.right_offset='350%'
        },
        //隐藏按钮
        btn_hover_hidden(){
            this.left_offset='-500%'
            this.right_offset='500%'
        },
        //选择显示图片
        switch_image(index){
            this.distance=index*-100
        }
    },
    created(){
        //不使用按钮动画，直接进行显示按钮
        if(this.btn_show==false){this.left_offset='-350%';this.left_offset='350%';}
        //传入a默认标签不跳转
        this.swiper_imageLink=this.imageLink.splice(0,0)
        if(this.swiper_imageLink.length==0){for(let i=0;i<this.image.length;i++){this.swiper_imageLink.push('javascript:;')}}
        //传入显示器颜色
        this.inditor_show_color.push(this.indicator_hover_color)
        for(let i=0;i<this.image.length-1;i++){this.inditor_show_color.push(this.indicator_color)}

    },
    watch:{
        distance:{handler(){
            let color=this.indicator_hover_color
            let color_index=this.inditor_show_color.findIndex((value,index)=>{return value==color})
            this.inditor_show_color[color_index]=this.indicator_color
            let update_color=Math.abs(this.distance)/100
            console.log(update_color)
            this.inditor_show_color[update_color]=color
            console.log(this.inditor_show_color)
        },
        immediate:false,}
    }
}
</script>
<style scoped lang="less">
*{
    margin: 0px;
    padding:0px;
}
.swiper{
    width: 100%;
    height:100%;
    background-color: blanchedalmond;
    overflow: hidden;
      .left_button,.right_button{
        color:rgba(255,255,255,0.5);
        display: inline-block;
        position: relative;
        z-index: 999;
        font-size: var(--font_size);
        border:none;
        box-shadow: 0px 0px 1px 2px rgba(0,0,0,0.1);
        border-radius: 50%;
        width: 10%;
        height: 15%;
        background: rgba(0,0,0,0.5);
        transition: all 0.5s;
    }
    .left_button:hover,.right_button:hover{
         background: rgba(0,0,0,0.8);
    }
    .left_button{
        transform:translate(var(--left_offset),-350%);
    }
    .right_button{
        transform:translate(var(--right_offset),-350%);
    }
    .inditor{
        margin: 0 auto;
        position: relative;
        top:-15%;
        left:0px;
        height: 2%;
        border-radius: 10%;
        z-index: 999;
        transform: translate(0,-300%);
        display:flex;
        justify-content: center;
    }
    .inditor li{
        list-style-type: none;
        width: var(--li_width);
        height: 100%;
        display: inline-block;
        background-color:var(--indicator_color);
        margin:0px 1%;
        max-width: 5%;
        &:hover{
            background-color: rgba(0,0,0,0.6);
        }
    }
}

.content{
    width: 100%;
    height: 100%;
    white-space: nowrap;
    overflow: hidden;
    font-size:0;
    a {
        transition:all;
        transition-duration: var(--animation_time);
        display: inline-block;
        width: 100%;
        height: 100%;
        position: relative;
        left: var(--position);
        img{
        width: 100%;
        height: 100%;
        }
    }
  
}
</style>