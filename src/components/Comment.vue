
<template>
    <span class="comment" ref="commentDom">{{comment}}</span>
</template>
<script lang="ts">
import { defineComponent, ref, onBeforeUnmount, onMounted, watch } from "vue";

export default defineComponent({
    props: {
        id: {
            type: String,
            required: true,
        },
        comment: {
            type: String,
            required: true,
        },
        x: {
            type: Number,
            required: true,
        },
        y: {
            type: Number,
            required: true,
        },
        color: {
            type: String,
            required: true,
        },
    },

    emits: ['removeDisplay'],

    setup(props, {emit}) {
        const positionX = ref(props.x);
        const positionY = ref(props.y);
        const intervalId = ref();
        const width = ref(0);
        const commentDom = ref<HTMLImageElement>();

        intervalId.value = setInterval(() => {
            positionX.value = positionX.value - 1;
        },1); 

        onBeforeUnmount(() => {
            clearInterval(intervalId.value);
        });

        watch(positionX, () => {
            if (positionX.value * -1 == commentDom.value?.clientWidth) {
                emit('removeDisplay', props.id);
            }
        });

        return {positionX, positionY, intervalId,commentDom}
    },
})
</script>
<style scoped>
.comment {
    color: v-bind(color);
    position: absolute;
    top: v-bind(positionY + "px");
    left: v-bind(positionX + "px");
    font-size: 30px;
    font-weight: 800;
    text-shadow: 1px 1px 1px #000000, -1px 1px 1px #000000, 1px -1px 1px #000000, -1px -1px 1px #000000, 1px 0px 1px #000000, 0px 1px 1px #000000, -1px 0px 1px #000000, 0px -1px 1px #000000;
}

</style>


