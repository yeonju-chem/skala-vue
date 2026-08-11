<script setup>
import { ref } from 'vue'

// 1. 학습환경 구성
import SampleOne from './components/practices/basic/SampleOne.vue'
import SampleTwo from './components/practices/basic/SampleTwo.vue'

// 2. Vue Directive
import VueHtml from './components/practices/basic/VueHtml.vue'
import VueHtmlXss from './components/practices/basic/VueHtmlXss.vue'
import VueText from './components/practices/basic/VueText.vue'
import VueBind from './components/practices/basic/VueBind.vue'
import VueBindClass from './components/practices/basic/VueBindClass.vue'
import VueBindStyle from './components/practices/basic/VueBindStyle.vue'
import VueBindShorthand from './components/practices/basic/VueBindShorthand.vue'
import VueIf from './components/practices/basic/VueIf.vue'
import VueShow from './components/practices/basic/VueShow.vue'
import VueFor from './components/practices/basic/VueFor.vue'
import VuePre from './components/practices/basic/VuePre.vue'
import VueCloak from './components/practices/basic/VueCloak.vue'
import VueOnce from './components/practices/basic/VueOnce.vue'
import VueMemo from './components/practices/basic/VueMemo.vue'

// 3. Vue Event Handling
import EventBasic from './components/practices/basic/EventBasic.vue'
import EventObject from './components/practices/basic/EventObject.vue'
import EventModifier from './components/practices/basic/EventModifier.vue'
import EventModifierPractice from './components/practices/basic/EventModifierPractice.vue'

// 4. Vue Form Handling
import ModelBasic from './components/practices/basic/ModelBasic.vue'
import ModelForm from './components/practices/basic/ModelForm.vue'
import ModelModifier from './components/practices/basic/ModelModifier.vue'

// 5. Vue Style Handling
import StyleScoped from './components/practices/basic/StyleScoped.vue'

// 6. Composition API
import ReactiveRef from './components/practices/composition/ReactiveRef.vue'
import ReactiveReactive from './components/practices/composition/ReactiveReactive.vue'
import ComputedBasic from './components/practices/composition/ComputedBasic.vue'
import WatchersBasic from './components/practices/composition/WatchersBasic.vue'
import WatchersMulti from './components/practices/composition/WatchersMulti.vue'
import WatchersDeep from './components/practices/composition/WatcherDeep.vue'
import WatchersReactive from './components/practices/composition/WatchersReactive.vue'
import WatchersRefArray from './components/practices/composition/WatchersRefArray.vue'
import WatchersReactiveArray from './components/practices/composition/WatchersReactiveArray.vue'
import WatchersWatchEffect from './components/practices/composition/WatchersWatchEffect.vue'

// 과제 (Hands on) - weather mockup
import WeatherMockup from './components/exercise/WeatherMockup.vue'

// 화면 상단 스위치로 "practice"와 "exercise"를 완전히 분리해서 보여준다.
// (나중에 Vue Router를 배우면 이 부분을 실제 페이지 라우팅으로 바꿀 예정)
const mode = ref('practice') // 'practice' | 'exercise'
</script>

<template>
  <nav class="mode-switcher" :class="{ 'exercise-mode': mode === 'exercise' }">
    <button class="mode-btn" :class="{ active: mode === 'practice' }" @click="mode = 'practice'">
      Vue 문법 실습
    </button>
    <button class="mode-btn" :class="{ active: mode === 'exercise' }" @click="mode = 'exercise'">
      과제 (Hands on)
    </button>
  </nav>

  <div v-if="mode === 'practice'" class="practice-container">
    <h1>1. 학습환경구성</h1>
    <hr />
    <SampleOne />
    <SampleTwo />
    <h1>2. Vue Directive</h1>
    <hr />
    <VueHtml />
    <VueHtmlXss />
    <VueText />
    <VueBind />
    <VueBindClass />
    <VueBindStyle />
    <VueBindShorthand />
    <VueIf />
    <VueShow />
    <VueFor />
    <VuePre />
    <VueCloak />
    <VueOnce />
    <VueMemo />
    <h1>3. Vue Event Handling</h1>
    <hr />
    <EventBasic />
    <EventObject />
    <EventModifier />
    <EventModifierPractice />
    <h1>4. Vue Form Handling</h1>
    <hr />
    <ModelBasic />
    <ModelForm />
    <ModelModifier />
    <h1>5. Vue Style Handling</h1>
    <hr />
    <StyleScoped />
    <h1>6. Composition API</h1>
    <hr />
    <ReactiveRef />
    <ReactiveReactive />
    <ComputedBasic />
    <WatchersBasic />
    <WatchersMulti />
    <WatchersDeep />
    <WatchersReactive />
    <WatchersRefArray />
    <WatchersReactiveArray />
    <WatchersWatchEffect />
  </div>

  <WeatherMockup v-else />
</template>

<style>
@import '@/assets/practice.css';

.mode-switcher {
  position: fixed;
  top: 14px;
  right: 16px;
  z-index: 999;
  display: flex;
  gap: 6px;
  padding: 5px;
  border-radius: 999px;
  background: rgba(20, 24, 33, 0.55);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.16);
}
.mode-switcher.exercise-mode {
  top: auto;
  right: 50%;
  bottom: 14px;
  transform: translateX(50%);
  background: rgba(8, 17, 31, 0.82);
  box-shadow: 0 8px 30px rgba(0, 0, 0, 0.28);
}
@media (max-width: 760px) {
  .mode-switcher.exercise-mode {
    right: 10px;
    bottom: 10px;
    transform: none;
    opacity: 0.46;
    transition: opacity 0.2s ease;
  }
  .mode-switcher.exercise-mode:hover,
  .mode-switcher.exercise-mode:focus-within {
    opacity: 1;
  }
  .mode-switcher.exercise-mode .mode-btn {
    padding: 5px 7px;
    font-size: 9px;
  }
}
.mode-btn {
  padding: 7px 14px;
  border: none;
  border-radius: 999px;
  background: transparent;
  color: #fff;
  font-size: 12px;
  cursor: pointer;
  transition: all 0.15s ease;
}
.mode-btn:hover {
  background: rgba(255, 255, 255, 0.14);
}
.mode-btn.active {
  background: #fff;
  color: #16233c;
  font-weight: 700;
}
</style>
