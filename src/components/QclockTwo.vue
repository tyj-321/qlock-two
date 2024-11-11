<script setup lang="ts">
import { computed, ref } from 'vue'
const letters = ref('ITLISASAMPM'+
                'ACQUARTERDC'+
                'TWENTYFIVEX'+
                'HALFSTENFTO'+
                'PASTERUNINE'+
                'ONESIXTHREE'+
                'FOURFIVETWO'+
                'EIGHTELEVEN'+
                'SEVENTWELVE'+
                'TENSEOCLOCK')
const lettersArray = computed(() => {
  let lettersArray = []
  letters.value.split('').forEach(element => {
    lettersArray.push({
      letter: element,
      active: false
    })
  })
  return lettersArray
})
const time = new Date()
const hours = time.getHours()
const minutes = time.getMinutes()
const matchIT = /IT/g
const matchResult = letters.value.match(matchIT)
if (matchResult) {
  const startIndex = letters.value.indexOf('IT')
  lettersArray.value[startIndex].active = true
  lettersArray.value[startIndex + 1].active = true
}
const matchIS = /IS/g
const matchResultIS = letters.value.match(matchIS)
if (matchResultIS) {
  const startIndex = letters.value.indexOf('IS')
  lettersArray.value[startIndex].active = true
  lettersArray.value[startIndex + 1].active = true
}



</script>

<template>
  <div class="w-full h-full flex justify-center items-center break-all">
    <div class="w-390px h-390px flex flex-wrap break-all">
      <span
        v-for="letter in lettersArray"
        :key="letter"
        class="h-35px w-35px flex justify-center items-center text-24px text-#484848"
        :class="{'text-#fff': letter.active}"
        :style="{'text-shadow': letter.active ? '0 0 10px #fff' : 'none'}"
      >
        {{ letter.letter }}
      </span>
    </div>
  </div>
</template>

<style scoped>
</style>
