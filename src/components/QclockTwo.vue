<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue'
import { hourMap, minuteMap } from './constant'
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
const timer = ref<number | null>(null)
const lettersArray = ref<{ letter: string, active: boolean }[]>([])

letters.value.split('').forEach((element: string) => {
  lettersArray.value.push({
    letter: element,
    active: false
    })
})

const updateClock = () => {
  const time = new Date()
  let hours = time.getHours()
  const minutes = time.getMinutes()
  
  // 重置所有字母的激活状态
  lettersArray.value.forEach(item => item.active = false)
  
  // 设置 "IT IS"
  const matchIT = /IT/g
  const matchIS = /IS/g
  ;[['IT', matchIT], ['IS', matchIS]].forEach(([word, pattern]) => {
    if (letters.value.match(pattern)) {
      const startIndex = letters.value.indexOf(word as string)
      lettersArray.value[startIndex].active = true
      lettersArray.value[startIndex + 1].active = true
    }
  })

  // 原有的时间显示逻辑
  const matchAM = /AM/g
  const matchPM = /PM/g
  const matchResultAM = letters.value.match(matchAM)
  const matchResultPM = letters.value.match(matchPM)

  if (hours < 12) {
    if (matchResultAM) {
      const startIndex = letters.value.indexOf('AM')
      lettersArray.value[startIndex].active = true
      lettersArray.value[startIndex + 1].active = true
    }
  } else {
    if (matchResultPM) {
      const startIndex = letters.value.indexOf('PM')
      lettersArray.value[startIndex].active = true
      lettersArray.value[startIndex + 1].active = true
    }
    hours = hours - 12
  }

  const hour = hourMap[hours as keyof typeof hourMap]
  const matchHour = new RegExp(hour, 'g')
  const matchResultHour = letters.value.match(matchHour)
  if (matchResultHour) {
    const startIndex = letters.value.indexOf(hour)
    if(minutes > 0 && minutes <= 30) {
      for (let i = 0; i < hour.length; i++) {
        lettersArray.value[startIndex + i].active = true
      }
    } else if(minutes > 30 && minutes < 60) {
      hours = hours + 1
      const hour = hourMap[hours as keyof typeof hourMap]
      const matchHour = new RegExp(hour, 'g')
      const matchResultHour = letters.value.match(matchHour)
      if (matchResultHour) {
        const startIndex = letters.value.indexOf(hour)
        for (let i = 0; i < hour.length; i++) {
          lettersArray.value[startIndex + i].active = true
        }
      }
    } else if(minutes === 0) {
      const matchOClock = /OCLOCK/g
      const matchResultOClock = letters.value.match(matchOClock)
      if (matchResultOClock) {
        const startIndexOClock = letters.value.indexOf('OCLOCK')
        for (let i = 0; i < 'OCLOCK'.length; i++) {
          lettersArray.value[startIndexOClock + i].active = true
        }
      }
    }
  }

  let minute = Math.floor(minutes/5)
  const minuteStr = minuteMap[minute as keyof typeof minuteMap]
  const matchMinute = new RegExp(minuteStr, 'g')
  const matchResultMinute = letters.value.match(matchMinute)
  if (matchResultMinute) {
    const startIndex = letters.value.indexOf(minuteStr)
    for (let i = 0; i < minuteStr.length; i++) {
      lettersArray.value[startIndex + i].active = true
    }
    if(minutes >= 5 && minutes < 30) {
      const matchPast = /PAST/g
      const matchResultPast = letters.value.match(matchPast)
      if (matchResultPast) {
        const startIndexPast = letters.value.indexOf('PAST')
        for (let i = 0; i < 'PAST'.length; i++) {
          lettersArray.value[startIndexPast + i].active = true
        }
      }
    } else if(minutes === 30) {
      const matchPast = /PAST/g
      const matchResultPast = letters.value.match(matchPast)
      if (matchResultPast) {
        const startIndexPast = letters.value.indexOf('PAST')
        lettersArray.value[startIndexPast].active = true
      }
      const matchHalf = /HALF/g
      const matchResultHalf = letters.value.match(matchHalf)
      if (matchResultHalf) {
        const startIndexHalf = letters.value.indexOf('HALF')
        lettersArray.value[startIndexHalf].active = true
      }
    } else if(minutes > 30) {
      const matchTo = /TO/g
      const matchResultTo = letters.value.match(matchTo)
      if (matchResultTo) {
        const startIndexTo = letters.value.indexOf('TO')
        for (let i = 0; i < 'TO'.length; i++) {
          lettersArray.value[startIndexTo + i].active = true
        }
      }
    }
  }
}

onMounted(() => {
  updateClock()
  timer.value = setInterval(updateClock, 1000)
})

onBeforeUnmount(() => {
  if (timer.value) {
    clearInterval(timer.value)
  }
})
</script>

<template>
  <div class="w-full h-full flex justify-center items-center break-all">
    <div class="w-390px h-390px flex flex-wrap break-all">
      <span
        v-for="(letter, index) in lettersArray"
        :key="index"
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
