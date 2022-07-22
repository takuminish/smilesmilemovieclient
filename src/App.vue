<template>
  <span v-for="comment in commentList" v-bind:key="comment._id">
    <Comment :id=comment._id :comment=comment.comment :x=comment.x :y=comment.y :color=comment.color @removeDisplay="onRemoveDisplay($event)" />
  </span>
</template>

<script lang="ts">
import { defineComponent, onMounted, onUnmounted, ref } from "vue";
import Comment from "./components/Comment.vue";
import {CommentModel} from "./model/Comment";
import axios from 'axios';


export default defineComponent({
  name: 'App',
  components: {
    Comment,
  },

  setup() {
    const intervalId = ref();
    const commentList = ref<CommentModel[]>([]);
    const windowWidth= ref(window.innerWidth);
    const windowHeight= ref(window.innerHeight);

    onMounted(() => {
      window.addEventListener('resize', onWidthChange);
    })

      onUnmounted(()=> window.removeEventListener('resize', onWidthChange));

    const onRemoveDisplay = (id: string) => {
      commentList.value = commentList.value.filter(comment => comment._id !== id);
    }

    const onWidthChange = () => {
      windowWidth.value= window.innerWidth;
      windowHeight.value= window.innerHeight;
    }


    intervalId.value = setInterval(() => {
      // ここにaxiosの処理を記述する
      axios.get<CommentModel[]>('https://smilesmilemovie.herokuapp.com/comments?token=${token}').then(response => {
        let list: CommentModel[] = response.data;
        list = list.map(l => {
          l.x = windowWidth.value;
          l.y = Math.floor( Math.random() * (windowHeight.value -100) );
          return l;
        });
      commentList.value = [...commentList.value, ...list];
      });
    },1000); 

    return {commentList, intervalId, onRemoveDisplay, onWidthChange}
  }

});
</script>

<style>

</style>
