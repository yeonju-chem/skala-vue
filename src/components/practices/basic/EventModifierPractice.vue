<script setup>
import { ref } from 'vue'

const onceCount = ref(0)
const selfMessage = ref('')
const enterMessage = ref('')
const ctrlMessage = ref('')

const handleOnce = () => {
  onceCount.value++
}

const handleSelf = () => {
  selfMessage.value = '회색 배경(부모)을 직접 클릭했습니다!'
}

const handleEnter = () => {
  enterMessage.value = '엔터 키로 전송되었습니다!'
}

const handleCtrlClick = () => {
  ctrlMessage.value = 'Ctrl + 클릭이 감지되었습니다!'
}
</script>

<template>
  <div class="practice-section">
    <h2>이벤트 수식어 추가 연습</h2>

    <h3>1) .once (딱 한 번만 실행)</h3>
    <p>클릭된 횟수: {{ onceCount }} (버튼 여러 번 눌러도 1까지만 증가)</p>
    <button @click.once="handleOnce">한 번만 반응하는 버튼</button>

    <h3>2) .self (자기 자신을 직접 클릭했을 때만)</h3>
    <div @click.self="handleSelf" style="padding: 20px; background-color: #ddd">
      <p>회색 배경 (여기 직접 클릭하면 반응)</p>
      <button>내부 버튼 (눌러도 반응 안 함)</button>
    </div>
    <p>{{ selfMessage }}</p>

    <h3>3) .enter (엔터 키 입력)</h3>
    <input type="text" @keyup.enter="handleEnter" placeholder="입력 후 엔터 눌러보세요" />
    <p>{{ enterMessage }}</p>

    <h3>4) .ctrl (Ctrl 키 + 클릭)</h3>
    <button @click.ctrl="handleCtrlClick">Ctrl 누르고 클릭해보세요</button>
    <p>{{ ctrlMessage }}</p>
  </div>
</template>
