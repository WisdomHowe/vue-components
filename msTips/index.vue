<template>
    <div class="ms-tips" v-show="isShow">
        <div class="tips-head">
            <span class="tips-title">提示</span>
            <span class="tips-close" @click="close"></span>
        </div>
        <div class="tips-main">{{ value }}</div>
    </div>
</template>

<script>
export default {
    name: "ms-tips",
    data() {
        return {
            value: "",          // 提示内容
            isShow: false       // 十分显示提示弹窗
        }
    },
    methods: {
        // 关闭提示
        close() {
            this.isShow = false;
            this.value = "";
        },
        // 打开提示
        open(value) {
            this.value = value;
            this.isShow = true;
        }
    }
}
</script>

<style lang="scss" scoped>
.ms-tips {
    position: fixed;
    left: 50%;
    top: 50%;
    transform: translateX(-50%) translateY(-50%);
    width: 370px;
    background: white;
    font-size: 15px;
    z-index: 9999;
    box-shadow: 0px 0px 10px #999;
    font-family: auto;

    .tips-head {
        position: relative;
        width: 100%;
        height: 40px;
        line-height: 40px;
        background: #c8161d;
        padding: 0 15px;
        box-sizing: border-box;
    }

    .tips-title {
        color: white;
    }

    .tips-close {
        position: absolute;
        right: 7.5px;
        top: 5px;
        display: block;
        width: 30px;
        height: 30px;
        cursor: pointer;
        background: url("./images/close.png") no-repeat;
        background-size: 15px 15px;
        background-position: center center;
    }

    .tips-main {
        width: 100%;
        padding: 30px 15px;
        box-sizing: border-box;
        text-align: center;
        color: #666666;
    }
    
}
</style>