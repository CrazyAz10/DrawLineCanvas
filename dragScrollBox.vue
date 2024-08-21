<template>
    <!-- 通用 容器内容区域内横向拖拽滑动横向滚动条组件 -->
    <div 
        class="drag-scroll-wrap" 
        >
        <div class="drag-scroll-box" ref="dragScrollBox">
            <slot></slot>
        </div>
        <div 
            class="mask" 
            @mousedown.stop="mousedown" 
            @mousemove.stop="mousemove" 
            @mouseup.stop="mouseup"></div>
    </div>
</template>

<script setup>
import { reactive, ref, computed, onMounted, nextTick } from "vue"
const dragScrollBox = ref(null)
const isDrag = ref(false)
let startXY = reactive([])
const mousedown = (event) => {
    isDrag.value = true
    startXY = [event.x, event.y]
}
const mousemove = (event) => {
    throttle((event) => {
        if (isDrag.value) {
            // 拖拽滑动开始
            let speadX = startXY[0] - event.x
            if (speadX >= 0) {
                if ((dragScrollBox.value.scrollLeft + dragScrollBox.value.offsetWidth + speadX) >= dragScrollBox.value.scrollWidth) {
                    dragScrollBox.value.scrollTo(dragScrollBox.value.scrollWidth - dragScrollBox.value.offsetWidth, 0)
                } else {
                    dragScrollBox.value.scrollTo(dragScrollBox.value.scrollLeft + speadX, 0)
                }
            } else {
                if ((dragScrollBox.value.scrollLeft + speadX) <= 0) {
                    dragScrollBox.value.scrollTo(0, 0)
                } else {
                    dragScrollBox.value.scrollTo(dragScrollBox.value.scrollLeft + speadX, 0)
                }
            }
            startXY = [event.x, event.y]
        }
    }, event, 50)
}
const mouseup = (event) => {
    isDrag.value = false
}

const valid = ref(true);
const throttle = (fn, event, delay) => {
    /* 一开始就触发---后续每隔delay时间内只触发一次 */
    if (valid.value) {
        valid.value = false; // 关闭阀门
        // 如果阀门已经打开，就继续往下
        setTimeout(() => {
            fn(event); // 定时器结束后执行
            valid.value = true; // 执行完成后打开阀门
        }, delay);
    }
}
</script>

<style lang="scss" scoped>
.drag-scroll-wrap{
    position: relative;
    overflow: hidden;
    .drag-scroll-box{
        width: 100%;
        overflow: auto;
    }
    .mask{
        position: absolute;
        bottom: 16px;
        left: 0;
        right: 0;
        top: 0;
        z-index: 99999;
        cursor: grab;
        &:active{
            cursor: grabbing;
        }
    }
}
</style>