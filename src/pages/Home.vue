<script lang="ts">
import { defineComponent, ref } from 'vue'
import AnswerInput from '../components/AnswerInput.vue'
import db from '../database'

function mapQuestions(questions: typeof db) {
  return questions.map(q => ({
    ...q,
    templatePhrase: q.templatePhrase.split('{unknown}'),
    userAnswer: null as string,
    isCorrect: undefined as boolean
  }))
}

export default defineComponent({
  name: 'Home',
  components: {
    AnswerInput
  },
  setup() {
    const questions = ref(mapQuestions(db))

    const submit = () => {
      for(let q of questions.value) {
        if(q.possibleAnswers.includes(q.userAnswer.trim())) {
          q.isCorrect = true
        } else {
          q.isCorrect = false
        }
      }
    }

    return {
      questions,
      submit
    }
  }
})
</script>

<template>
  <form @submit.prevent="submit()" class="container mx-auto pt-16">
    <div
      v-for="(q, i) of questions" :key="i"
      class="flex flex-col p-4 rounded border border-gray-200 mb-8"
      :class="{
        'border-red-500': q.isCorrect === false,
        'border-green-500': q.isCorrect === true
      }"
    >
      <span class="uppercase text-sm font-bold text-gray-600 mb-4">Exercise {{ i + 1 }}</span>
      <p>{{ q.basePhrase }}</p>

      <hr class="my-4">

      <div class="flex flex-col">
        <p class="mb-2 font-bold">{{ q.baseWord }}</p>
        <p class="flex flex-row items-end">
          <span>{{ q.templatePhrase[0] }}</span>
          <span>
            <answer-input v-model="q.userAnswer" :slug="q.baseWord" :label="q.baseWord" class="inline" />
          </span>
          <span>{{ q.templatePhrase[1] }}</span>
        </p>
      </div>
      <div v-if="q.isCorrect" class="flex flex-col mt-4">
        <span class="font-bold text-green-500">Your answer was correct!</span>
        <p>Possible answers were: <span class="italic">{{ q.possibleAnswers.join('; ') }}</span></p>
      </div>
      <div v-if="q.isCorrect === false" class="flex flex-col mt-4">
        <span class="font-bold text-red-500">Your answer was wrong.</span>
        <p>Possible answers include: <span class="italic">{{ q.possibleAnswers.join('; ') }}</span></p>
        <p class="mt-2">
          {{ q.explanation }}
        </p>
      </div>
    </div>
    <div class="flex flex-row space-x-4">
      <button type="reset" class="px-4 py-2 bg-gray-200 rounded hover:bg-gray-300">Clear</button>
      <button type="submit" class="px-4 py-2 bg-green-500 rounded hover:bg-green-600">
        Submit
      </button>
    </div>
  </form>
</template>