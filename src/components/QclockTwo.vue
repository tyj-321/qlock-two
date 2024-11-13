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

function renderLetters(letter: string) {
  const matchLetter = new RegExp(letter, 'g')
  const matchResultLetter = letters.value.match(matchLetter)
  if (matchResultLetter) {
    const startIndex = letters.value.indexOf(letter)
    if(!lettersArray.value[startIndex].active) {
      for (let i = 0; i < letter.length; i++) {
        lettersArray.value[startIndex + i].active = true
      }
    }
  }
}

const updateClock = () => {
  const time = new Date()
  const hours = time.getHours()
  const minutes = time.getMinutes()
  
  // 重置所有字母的激活状态
  lettersArray.value.forEach(item => item.active = false)
  
  // 设置 "IT IS"
  renderLetters('IT')
  renderLetters('IS')

  // 处理上午/下午显示
  const isAM = hours < 12
  renderLetters(isAM ? 'AM' : 'PM')

  // 处理小时显示
  let displayHour = hours % 12 || 12
  if(minutes >= 35) {
    displayHour = (displayHour + 1) % 12 || 12
  }
  const hourStr = hourMap[displayHour as keyof typeof hourMap]

  // 处理特殊小时显示位置
  const renderSpecialHour = () => {
    if(hourStr === 'TEN') {
      for (let i = 0; i < 3; i++) {
        lettersArray.value[99 + i].active = true
      }
    } else if(hourStr === 'FIVE') {
      for (let i = 0; i < 4; i++) {
        lettersArray.value[70 + i].active = true
      }
    } else {
      renderLetters(hourStr)
    }
  }

  // 根据不同时间段处理小时显示
  if(minutes < 35 || minutes >= 50) {
    renderSpecialHour()
  } else if(minutes >= 35) {
    if((hourStr === 'TEN' || hourStr === 'ELEVEN') && minutes > 55) {
      for (let i = 0; i < 4; i++) {
        lettersArray.value[70 + i].active = true
      }
    } else {
      renderSpecialHour()
    }
  }

  // 处理分钟显示
  const minute = Math.floor(minutes/5)
  const minuteStr = minuteMap[minute as keyof typeof minuteMap]

  if(minutes === 0) {
    renderLetters('OCLOCK')
  } else {
    // 处理特殊分钟显示
    if(hourStr === 'FIVE' && minutes >= 55) {
      for (let i = 0; i < 4; i++) {
        lettersArray.value[28 + i].active = true
      }
    } else if(minutes >= 35 && minutes < 55) {
      if((hourStr === 'TEN' || hourStr === 'ELEVEN') && minuteStr === 'TEN') {
        for (let i = 0; i < 3; i++) {
          lettersArray.value[99 + i].active = true
        }
      }
      renderLetters(minuteStr)
    } else {
      renderLetters(minuteStr)
    }
    
    // 显示 PAST/TO
    renderLetters(minutes < 35 ? 'PAST' : 'TO')

    // 处理 QUARTER 和 HALF
    if(minute === 3 || minute === 9) {
      renderLetters('QUARTER')
    } else if(minute === 6) {
      renderLetters('HALF')
    }
  }

  // 处理特殊情况的显示
  if(hourStr === 'TEN' && minuteStr === 'FIVE') {
    for (let i = 0; i < 3; i++) {
      lettersArray.value[99 + i].active = true
    }
  }

  if(hourStr === 'FIVE' && minuteStr === 'FIVE') {
    for (let i = 0; i < 4; i++) {
      lettersArray.value[70 + i].active = true
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
