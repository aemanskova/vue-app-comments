<script setup>
import { ref, computed } from '@vue/reactivity'

const name = ref('')
const comment = ref('')
const reaction = ref(0)
const disabledButton = ref(false)
const emit = defineEmits()

const isFormValid = computed(() => {
  return name.value !== '' && comment.value !== ''
})

const props = defineProps({
  parentId: {
    type: Number,
    required: false
  },
  show: {
    type: Boolean,
    required: false
  }
})

async function send() {
  disabledButton.value = true
  try {
    let data = {
      author: name.value,
      text: comment.value,
      reaction: reaction.value,
      parentId: props.parentId
    }

    let response = await fetch('http://slavaver.space/comments', {
      headers: {
        'Content-Type': 'application/json',
        Accept: 'application/json',
        Username: 'aemanskova'
      },
      method: 'POST',
      body: JSON.stringify(data)
    })

    let res = await response.json()
    console.log(res)
    console.log(data)
    emit('update:show', false)
    name.value = ''
    comment.value = ''
    reaction.value = 0
  } catch (err) {
    console.log(err)
  } finally {
    disabledButton.value = false
  }
}
</script>

<template>
  <form class="form" v-if="props.show">
    <div class="form__group">
      <label for="name">Имя</label>
      <input class="form__input" type="text" name="name" v-model="name" />
    </div>

    <div class="form__group">
      <label for="comment">Комменарий</label>
      <textarea class="form__input" name="comment" cols="30" rows="10" v-model="comment"></textarea>
    </div>

    <fieldset class="form__group" id="reaction">
      <legend>Выбери смайлик</legend>
      <input
        class="form__checkbox"
        id="negative"
        type="radio"
        v-model="reaction"
        value="1"
        name="reaction"
      />
      <label tabindex="0" class="form__reaction" for="negative"
        ><img src="./icons/angry.svg" alt=""
      /></label>

      <input
        class="form__checkbox"
        id="default"
        type="radio"
        v-model="reaction"
        value="0"
        name="reaction"
      />
      <label tabindex="0" class="form__reaction" for="default"
        ><img src="./icons/neutral.svg" alt=""
      /></label>

      <input
        class="form__checkbox"
        id="positive"
        type="radio"
        v-model="reaction"
        value="-1"
        name="reaction"
      />
      <label tabindex="0" class="form__reaction" for="positive"
        ><img src="./icons/slightly.svg" alt=""
      /></label>
    </fieldset>

    <button
      class="form__button"
      :disabled="!isFormValid || disabledButton"
      @click.prevent="send"
      type="submit"
    >
      Отправить
    </button>
    <p class="form__warning" v-if="!isFormValid">Заполните все поля!</p>
  </form>
</template>

<style scoped>
.form {
  display: flex;
  flex-direction: column;
  gap: 5px;
  justify-content: center;
  max-width: 360px;
  padding: 20px;
}

.form__warning {
  font-size: 0.275rem;
  letter-spacing: 0.05em;
  text-shadow: 1px 1px #00b4c5, -1px 1px #00b4c5, -1px -1px #00b4c5, 1px -1px #00b4c5;
  color: white;
}
.form__button {
  border: none;
  background-color: black;
  color: white;
  border-radius: 14px;
  padding: 10px;
  width: max-content;
}

.form__button:disabled {
  opacity: 0.5;
}
.form__group {
  display: flex;
  justify-content: space-between;
}
.form__input {
  max-width: 200px;
  width: 150px;
  border-radius: 14px;
  padding: 10px;
}
@media (min-width: 430px) {
  .form__input {
    width: 200px;
  }
}

.form__checkbox {
  display: none;
}
.form__checkbox:checked + label {
  box-shadow: 10px 10px 25px black;
  border-radius: 50%;
}
.form__reaction {
  display: flex;
  justify-content: center;
  align-items: center;
}

fieldset,
input,
textarea {
  border: none;
  padding: 0;
}

input,
textarea {
  outline: none;
}
</style>
