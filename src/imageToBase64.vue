<template>
    <div class="base64">
        <div  :class="['upload-cont',btnClass]">
            上传图片
            <input type="file" :multiple="multiple" class="base64-upload" @change='onChange' >
        </div>
        <img v-show='preview && previewImage' :src="preview" class="base64-image">
    </div>
</template>

<script>
/**
 * @file: 图片转base64
 * @Author: wangmeng
 * @Date:   2018-09-05
 * @description: 上传图片转base64 支持预览功能   @size-error 图片过大  @type-error 类型错误  file-info  图片base64的信息
 * props: accept --无效， previewImage支持图片预览，  maxSize 图片大小（M），multiple多文件上传 --todo 
 * @referenced: 目前用于 vue中图片转base64之后上传
 **/
    export default {
        name: 'image-to-base64',
        props: {
            accept: { // 图片类型
                type: String,
                default: 'image/png,image/png'
            },
            previewImage: {  //是否显示图片
                type: Boolean,
                default: true,
            },
            maxSize: {  // 图片大小限制
              type: Number,
              default: 5
            },
            multiple: {
                type: Boolean,
                default: false
            },
            btnClass: {
                type: String,
                default: '',
            }
        },
        data() {
            return {
                preview: null, //图片uri
            }
        },
        mounted() {

        },
        methods: {
            onChange(e) {
                console.log(e)
                const files = e.target.files || e.dataTransfer.files;
                if (!files.length) {
                    return;
               }
               //支持单个文件上传
                const file = files[0],
                M = 1024 * 1024,
                size = file.size / M,
                reg =  /\/(jpg|png|jpeg)+$/i,
                reader = new FileReader();
                let fileInfo = {};
                
                if (size > this.maxSize) {
                    this.$emit('size-error', size );
                    return;
                }
                if (!reg.test(file.type)) {
                    this.$emit('type-error',file.type)
                    return
                }
                reader.onload = e => {
                    const dataURI = e.target.result;
                    if (dataURI) {
                        fileInfo = {
                            name: file.name,
                            type: file.type,
                            size: Math.round(file.size / 1000)+' kB',
                            base64: dataURI,
                            file: file
                        }
                        this.$emit('file-info', fileInfo);
                        this.preview = dataURI;
                    }
                }
                reader.readAsDataURL(file)
                }
            },
        watch: {

        },
        computed: {

        }
    }
</script>

<style lang='less' scoped>
.base64 {
        .upload-cont {
        position: relative;
        width: 100px;
        height: 30px;
        border: 1px solid #64ACFF;
        border-radius: 4px;   
        text-align: center;
        line-height: 30px;
        >input[type=file] {
            position: absolute;
            top: 0;
            left: 0;
            height: 100%;
            width: 100px;
            opacity: 0;
        }
    }
    .base64-image {
        margin-top: 20px;
        width: 100px;
    }
}
</style>