<template>
    <transition name="slide-fade">
        <div class="add-file-frame">
            <div class="file-model" ref="fileModel">
                <div class="title" @mousedown="getMoveFile($event)" ref="header">
                    <span>{{ title }}</span>
                    <span class="close fa fa-window-close" @click.stop="modelClose()"></span>
                </div>
                <div class="operation">
                    <i class="fa fa-save" @click="saveText" title="保存"></i>
                </div>
                <div class="content">
                    <textarea class="content-text" 
                    contenteditable="true" 
                    ref="contentText" v-model="contentBuffer">
                    </textarea>
                </div>
            </div>
            <div class="file-fade"></div>
        </div>
    </transition>
</template>

<script>
    import * as path from 'path'
    import {MoveFrame} from '../../common/frameMove'
    export default {
        name: 'add-file-frame',
        props: ['fileItem', 'index'],
        data() {
            return {
                contentBuffer: '',
                isDraging: false
            }
        },
        computed: {
            getFileTextFlag() {
                return this.$store.state.fileTextFlag
            },
            title() {
                return path.posix.basename(this.fileItem.path)
            }
        },
        created() {
            this.contentBuffer = this.fileItem.content
        },
        methods: {
            modelClose() {
                this.$store.commit('removeFileEditorById', this.index)
            },
            saveText() {
                this.$store.commit('setFileEditorContentById', this.index, this.contentBuffer)
                this.$store.dispatch('saveFileEditorToFile', {
                    path: this.fileItem.path,
                    content: this.contentBuffer
                })
            },
            getMoveFile(event) {
                let model = this.$refs.fileModel
                let header = this.$refs.header
                MoveFrame(model, header, 1000, this.isDraging)
            }
        }
    }
</script>

<style lang="stylus" rel="stylesheet/stylus" type="text/stylus" scoped>
    .file-model
        opacity: 0.85
        position: fixed
        top: 50%
        left: 50%
        width: 50%
        max-width: 630px
        min-width: 320px
        height: auto
        z-index: 1000
        visibility: visible
        backface-visibility: visible
        transform: translateX(-50%) translateY(-50%)
        background #ffffff
        box-shadow: 0 0 6px 2px #97D9D7
        border 1px solid #ffffff
        .title
            position relative
            padding: 5px 0 5px 8px
            font-size: 14px
            color: white
            background: dimgrey
            cursor: default
            .close
                position: absolute
                top: 5px
                right: 8px
                font-size: 15px
                color: #ffffff
                cursor: pointer
        .operation
            padding 5px 0 5px 8px
            i
                margin: 0 10px
                font-size: 19px
            i:nth-child(1)
                margin: 0
        .content
            position: relative
            width: 100%;
            height: 230px
            overflow: hidden
            .content-text
                display: block
                position: absolute
                width: 99%
                left: 0
                right: 0
                top: 0
                overflow-y: scroll
                overflow-x: hidden
                z-index: 11
                padding: 0 0 0 5px
                height: 230px
                outline: none
                border: 0
                resize: none
                font-size: 14px
            .content-text::-webkit-scrollbar
                width: 10px
                background-color: #ffffff
            .content-text::-webkit-scrollbar-track
                background-color: #ffffff
            .content-text::-webkit-scrollbar-thumb
                background-color: #ada3ab
    .slide-fade-enter-active 
        transition: opacity .3s
    .slide-fade-leave-active
        transition: opacity .3s
    .slide-fade-enter, .slide-fade-leave-to
        opacity: 0
</style>