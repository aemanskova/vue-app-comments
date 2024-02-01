<script setup>
import { ref, onMounted, reactive } from 'vue'

import Form from './components/Form.vue'
import Comments from './components/Comments.vue'

const data = reactive({
  comments: [],
  source: null,
  NewComment: false
})

async function getData() {
  try {
    let response = await fetch('http://slavaver.space/comments', {
      method: 'GET'
    })

    data.comments = await response.json()
    data.comments.reverse()
    console.log(data.comments)
  } catch (err) {
    console.log('Error')
  }
}

getData()

function handleCheckboxChange(event) {
  if (event.target.checked) {
    data.source = new EventSource('http://194.67.93.117:80/comments/stream')
    console.log(data.source)

    data.source.addEventListener('message', (event) => {
      console.log(data.comments)
      const comment = JSON.parse(event.data)
      console.log(comment)
      if (!comment.parentId) {
        comment.parentId = null
      }
      data.comments.push(comment)
      console.log(data.comments)
      console.log('hi')
    })
  } else {
    data.source.close()
  }
}

console.log(data)
</script>



<template>
  <div class="comments">
    <div class="comments__container">
      <h1>Лабораторная работа №12, Манскова Алёна, 221-321</h1>
      <div class="comments__header">
        <span class="comments__text">Количество комментариев: {{ data.comments.length }}</span>
        <div class="comments__checkbox">
          <label class="comments__text" for="checkEventSource"
            >Получать комментарии в режиме реального времени
          </label>
          <input
            v-bind="$attrs"
            type="checkbox"
            id="checkEventSource"
            name="checkEventSource"
            @change="handleCheckboxChange"
            :checked="checked"
            class="comments__switch"
          />
        </div>
        <button class="comments__button" @click="data.NewComment = true">
          Создать новый комменарий
        </button>
      </div>
      <Form v-model:show="data.NewComment" :parent-id="null" />
      <div class="comments__list">
        <Comments :depth="1" :comments="data.comments" :parent-id="null" />
      </div>
    </div>
  </div>
</template>

<style scoped>
h1 {
  letter-spacing: 0.05em;
  text-shadow: 1px 1px #00b4c5, -1px 1px #00b4c5, -1px -1px #00b4c5, 1px -1px #00b4c5;
  color: white;
}

.comments__text {
  font-weight: 600;
}

.comments__list {
  width: max-content;
  margin: 0 auto;
}

.comments__switch {
  width: 48px;
  height: 24px;
  appearance: none;
  -webkit-appearance: none;
  -moz-appearance: none;
  background-color: black;
  border-radius: 12px;
  border: none;
  position: relative;
  margin-right: 10px;
}

.comments__switch:before {
  content: '';
  display: block;
  position: absolute;
  top: 2px;
  left: 2px;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background-color: #fff;
  transition: transform 0.3s;
}

.comments__switch:checked:before {
  transform: translateX(15px);
  background-color: #00b4c5;
}

@media (min-width: 420px) {
  .comments__switch:checked:before {
    transform: translateX(20px);
    background-color: #00b4c5;
  }
  .comments__header {
    gap: 30px;
  }
}

.comments {
  background-image: url(../src/components/img/background.jpg);
  background-repeat: repeat-y;
  overflow-x: hidden;
  background-size: contain;
  width: 100vw;
  height: 100%;
  font-size: 0.1875rem;
}

.comments__checkbox {
  display: flex;
  gap: 5px;
  align-items: center;
}
.comments__container {
  width: 100%;
  max-width: 1000px;
  margin: 0 auto;
  padding: 15px;
}
.comments__header {
  display: flex;
  flex-direction: column;
  gap: 10px;
  padding: 20px 0 20px 0;
}

.comments__button {
  border: none;
  background-color: black;
  color: white;
  border-radius: 14px;
  padding: 10px;
  width: max-content;
}
</style>
