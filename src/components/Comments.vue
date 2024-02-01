<script setup>
import { computed, ref } from '@vue/reactivity'

import Comments from './Comments.vue'
import Comment from './Comment.vue'

const props = defineProps({
  comments: {
    type: Array,
    default: () => []
  },
  parentId: [Number, null],
  depth: Number
})

const comments = computed(() => {
  return props.comments.filter((comment) => comment.parentId === props.parentId)
})

console.log(comments.length)
</script>

<template >
  <div v-for="comment in comments" :key="comment.id">
    <Comment
      class="comments"
      :comment="comment"
      :comments="props.comments.filter((c) => comment.id === c.parentId)"
      :countAnswers="props.comments.filter((c) => c.parentId == comment.id).length"
    />
    <div
      :class="{
        comments__answer_many: props.depth > 2
      }"
      class="comments__answer"
    >
      <Comments
        :depth="props.depth + 1"
        v-if="props.comments"
        :comments="props.comments"
        :parent-id="comment.id"
      />
    </div>
  </div>
</template>

<style scoped>
.comments {
  padding: 5px 0px;
}
.comments__answer {
  margin-left: 20px;
}

@media (min-width: 420px) {
  .comments__answer {
    margin-left: 30px;
  }
}

.comments__answer_many {
  margin-left: 0px;
}
</style>

