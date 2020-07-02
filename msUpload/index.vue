<template>
    <div class="ms-upload">
        <div class="box">
            <input ref="file" type="file" name="picture" @change="uploadfile">
            <div class="imgWrap" v-show="src !== ''" @click="showOpera">
                <img :src="showSrc">
            </div>
            <div class="loading" v-show="isLoading"></div>
        </div>
        <div class="opera" v-show="isShow">
            <div class="bg" @click="hideOpera"></div>
            <ul>
                <li>请选择</li>
                <li @click="showZoomImg">查看</li>
                <li @click="replaceImg">替换</li>
                <li @click="hideOpera">取消</li>
            </ul>
        </div>
        <div class="larger" v-show="isMore" @click="hideZoomImg">
            <div class="largerWrap">
                <img :src="showSrc" alt="">
            </div>
        </div>
    </div>
</template>

<script>
import axios from "axios";

export default {
    name: "ms-upload",
    props: {
        url: {                      // 请求地址
            type: String,
            default: ""
        },
        name: {                     // 图片上传字段名称
            type: String,
            default: "file"
        },
        pic: {                      // 上次成功后返回的图片地址
            type: String,
            default: ""
        },
        show: {                     // 本地生成的图片地址
            type: String,
            default: ""
        }
    },
    data() {
        return {
            isShow: false,          // 是否显示图片操作界面
            isMore: false,          // 是否查看放大图片
            isLoading: false,       // 图片是否上传加载中
            src: this.pic,          // 上传到后台成功返回的图片链接
            showSrc: this.show      // 显示在页面上的图片链接
        }
    },
    methods: {
        /**
         * 上传图片
         * @param {*} e 
         */
        uploadfile(e) {
            if (e.target.files.length === 0) {
                return;
            }
            const maxSize = 5242880; //图片限制5m
            const file = e.target.files[0];
            const limitType = ["image/jpeg", "image/png"];
            if (limitType.indexOf(file.type) === -1) {
                alert("请选择格式为*.jpg、*.png、*.jpeg 的图片");
                return;
            }
            if (file && file.size && file.size > maxSize) {
                alert("上传图片不能大于5M");
                return;
            }
            this.isLoading = true;
            const formData = new FormData();
            formData.append(this.name, file);
            if(!!this.url){
                alert("请配置图片上传接口路径");
                return;
            }
            axios.post(this.url, formData, {headers:{'Content-Type':'multipart/form-data'}}).then(rsp => {
                this.isLoading = false;
                this.src = rsp.data.data.fileName;
                this.showSrc = window.URL.createObjectURL(file);
            }).catch(() => {
                this.isLoading = false;
                this.src = "";
                this.showSrc = "";
                alert("上传失败, 请重新上传！");
            });
        },
        /**
         * 显示图片操作界面
         */
        showOpera() {
            this.isShow = true;
        },
        /**
         * 隐藏图片操作界面
         */
        hideOpera() {
            this.isShow = false;
        },
        /**
         * 显示放大查看图片
         */
        showZoomImg() {
            this.isMore = true;
            this.isShow = false;
        },
        /**
         * 隐藏放大查看图片
         */
        hideZoomImg() {
            this.isMore = false;
            this.isShow = true;
        },
        /**
         * 替换图片
         */
        replaceImg() {
            this.$refs.file.click();
            this.isShow = false;
        },
        /**
         * 返回图片地址
         */
        getImage() {
            return {
                src: this.src,
                show: this.showSrc
            };
        },
        /**
         * 重置参数
         */
        reset() {
            this.isShow = false;
            this.isMore = false;
            this.isLoading = false;
            this.src = "";
            this.showSrc = "";
        }
    },
    watch: {
        pic(val) {
            this.src = val;
        },
        show(val) {
            this.showSrc = val;
        }
    }
};
</script>

<style lang="scss" scoped>
.ms-upload {
    width: 255px;
    height: 170px;

    .box {
        position: relative;
        width: 100%;
        height: 100%;
        border: 1px solid #dadada;
        box-sizing: border-box;
        background-color: #fafafa;
        background-image: url("./images/add_btn.png");
        background-position: center center;
        background-repeat: no-repeat;

        input[type="file"] {
            position: absolute;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            z-index: 5;
            opacity: 0;
        }

        .loading {
            position: absolute;
            width: 100%;
            height: 100%;
            left: 0;
            top: 0;
            background: url("./images/loding.gif") no-repeat;
            background-size: 100% auto;
            background-color: white;
            background-position: center center;
            z-index: 7;
        }

        .imgWrap {
            position: relative;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
            z-index: 6;
            background-color: white;

            > img {
                position: absolute;
                left: 0;
                top: 0;
                bottom: 0;
                right: 0;
                margin: auto;
                width: 100%;
                height: auto;
            }
        }
    }

    .opera {
        position: relative;
        z-index: 10;

        .bg {
            position: fixed;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.5);
            cursor: pointer;
        }

        > ul {
            position: fixed;
            width: 280px;
            background: #fff;
            border-radius: 8px;
            text-align: center;
            left: 50%;
            top: 50%;
            transform: translate3d(-50%, -50%, 0);
            list-style: none;

            li {
                cursor: pointer;
                line-height: 24px;
                padding: 10px 0;
                font-size: 14px;
                color: #000;
                border-bottom: 1px solid #e1e1e1;
                margin-bottom: 0;

                &:first-child {
                    padding: 14px 0;
                    color: #a5a5a5;
                }

                &:last-child {
                    border-bottom: none;
                }
            }
        }
    }

    .larger {
        position: fixed;
        left: 0;
        top: 0;
        width: 100%;
        height: 100%;
        background: rgba(0, 0, 0, 0.5);
        z-index: 10;

        .largerWrap {
            position: fixed;
            left: 50%;
            top: 50%;
            transform: translate3d(-50%, -50%, 0);
            width: 600px;
            max-height: 600px;
            overflow-y: auto;

            img {
                width: 100%;
            }
        }
    }
}
</style>