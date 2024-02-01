<script setup>
import Form from './Form.vue'
import { ref, computed } from '@vue/reactivity'
import moment from 'moment'

const answer = ref(false)

const props = defineProps({
  comment: Object,
  countAnswers: Number,
  comments: Array
})

const reactions = computed(() => {
  return props.comments.reduce((acc, el) => (acc += +el.reaction), 0)
})
</script>

<template>
  <div>
    <div
      class="comment"
      :class="{
        comment_positive: reactions < 0,
        comment_negative: reactions > 0
      }"
    >
      <div class="comment__header">
        <span class="comment__author">{{ comment.author }}</span>
        <span>{{ moment(comment.createdAt).format('HH:mm DD.MM.YYYY') }}</span>
      </div>
      <div class="comment__body">
        <p>{{ comment.text }}</p>
        <div class="comment__elements">
          <button class="comment__button" @click="answer = true">Ответить</button>
          <span>Ответов: {{ countAnswers }}</span>
        </div>
      </div>
    </div>
    <Form v-model:show="answer" :parent-id="comment.id"></Form>
  </div>
</template>

<style scoped>
.comment {
  width: 250px;
  background-color: rgba(255, 255, 255, 0.756);
  border: 1px solid #00b4c5;
  border-radius: 14px;
  margin-top: 5px;
  margin-bottom: 5px;
  word-break: break-all;
  font-size: 0.1875rem;
}

@media (min-width: 420px) {
  .comment {
    width: 350px;
  }
}

@media (min-width: 750px) {
  .comment {
    width: 500px;
  }

  .comment__header {
    padding: 20px;
  }

  .comment__body {
    padding: 20px;
    gap: 20px;
  }
}



.comment_positive {
  background-color: #f2be22;
}

.comment_negative {
  background-color: #ff9b9b;
}

.comment__header {
  display: flex;
  flex-direction: column;
  background-color: #00b4c5;
  border-radius: 14px 14px 0 0;
  padding: 5px;
}

.comment__body {
  padding: 5px;
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.comment__elements {
  display: flex;
  justify-content: space-between;
}
.comment__author {
  font-weight: 600;
}

.comment__button {
  border: none;
  background-color: #00b4c5;
  border-radius: 14px;
}
</style>