# skala-vue

This template should help get you started developing with Vue 3 in Vite.

## Recommended IDE Setup

[VS Code](https://code.visualstudio.com/) + [Vue (Official)](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur).

## Recommended Browser Setup

- Chromium-based browsers (Chrome, Edge, Brave, etc.):
  - [Vue.js devtools](https://chromewebstore.google.com/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)
  - [Turn on Custom Object Formatter in Chrome DevTools](http://bit.ly/object-formatters)
- Firefox:
  - [Vue.js devtools](https://addons.mozilla.org/en-US/firefox/addon/vue-js-devtools/)
  - [Turn on Custom Object Formatter in Firefox DevTools](https://fxdx.dev/firefox-devtools-custom-object-formatters/)

## Customize configuration

See [Vite Configuration Reference](https://vite.dev/config/).

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Compile and Minify for Production

```sh
npm run build
```

### Lint with [ESLint](https://eslint.org/)

```sh
npm run lint
```

## Customization (2026-08-10)

- src/App.vue: HMR 확인용 초기 세팅, 이후 Vue Directive 실습용 컴포넌트들을 순차적으로 import/렌더링
- src/views/AboutView.vue: h1 텍스트 변경 (HMR 동작 확인)
- src/components/practices/basic/SampleOne.vue: 반응성(Reactivity) 예제 (일반 변수 vs ref)
- src/components/practices/basic/SampleTwo.vue: Text Interpolation 예제
- src/components/practices/basic/VueHtml.vue: v-html 디렉티브 학습
- src/components/practices/basic/VueHtmlXss.vue: v-html XSS 취약점 실습
- src/components/practices/basic/VueText.vue: v-text 디렉티브 학습
- src/components/practices/basic/VueBind.vue: v-bind 기본 (링크/이미지/버튼 비활성화)
- src/components/practices/basic/VueBindClass.vue: v-bind 클래스 바인딩 (객체/배열 형식)
- src/components/practices/basic/VueBindStyle.vue: v-bind 스타일 바인딩 (객체/배열 형식)
- src/components/practices/basic/VueBindShorthand.vue: v-bind same-name shorthand
- src/components/practices/basic/VueIf.vue: v-if / v-else-if / v-else
- src/components/practices/basic/VueShow.vue: v-show
- src/components/practices/basic/VueFor.vue: v-for (배열/객체/배열 내 객체 렌더링)

## Customization (2026-08-11)

### 2. Vue Directive (마무리)

- src/components/practices/basic/VuePre.vue: v-pre (컴파일 건너뛰기)
- src/components/practices/basic/VueCloak.vue: v-cloak (초기 렌더링 깜빡임 방지)
- src/components/practices/basic/VueOnce.vue: v-once (최초 1회만 렌더링)
- src/components/practices/basic/VueMemo.vue: v-memo (조건부 메모이제이션)

### 3. Vue Event Handling

- src/components/practices/basic/EventBasic.vue: v-on/@ 기본 이벤트 핸들링
- src/components/practices/basic/EventObject.vue: 이벤트 객체($event) 활용
- src/components/practices/basic/EventModifier.vue: 이벤트 모디파이어 (.prevent/.stop/.once 등)
- src/components/practices/basic/EventModifierPractice.vue: 이벤트 모디파이어 종합 실습

### 4. Vue Form Handling

- src/components/practices/basic/ModelBasic.vue: v-model 기본 양방향 바인딩
- src/components/practices/basic/ModelForm.vue: 다양한 폼 요소(select/checkbox/radio) v-model
- src/components/practices/basic/ModelModifier.vue: v-model 모디파이어 (.lazy/.number/.trim)

### 5. Vue Style Handling

- src/components/practices/basic/StyleScoped.vue: scoped 스타일과 외부 CSS(@import) 비교

### 6. Composition API

- src/components/practices/composition/ReactiveRef.vue: ref() 기초 (기본형/객체 반응형)
- src/components/practices/composition/ReactiveReactive.vue: reactive() 특징 및 배열 재할당 주의점
- src/components/practices/composition/ComputedBasic.vue: computed() 캐싱 동작 비교
- src/components/practices/composition/WatchersBasic.vue: watch() 기본 (단일 변수 감시)
- src/components/practices/composition/WatchersMulti.vue: watch() Multi-Source (배열로 여러 변수 동시 감시)
- src/components/practices/composition/WatcherDeep.vue: watch() Deep Watch (ref 객체, deep 옵션 vs 특정 속성 감시)
- src/components/practices/composition/WatchersReactive.vue: watch() + reactive 데이터 (자동 deep vs 특정 속성 감시)
- src/components/practices/composition/WatchersRefArray.vue: watch() + ref 배열의 특정 인덱스/요소 감시
- src/components/practices/composition/WatchersReactiveArray.vue: watch() + reactive 배열의 특정 인덱스/요소 감시
- src/components/practices/composition/WatchersWatchEffect.vue: watchEffect() 자동 의존성 감시

### 과제 (Hands on)

- src/components/exercise/WeatherMockup.vue: KBO 날씨 대시보드 (커스텀 과제)
  - 10개 구단 정보(커스텀 엠블럼, 승리 예측용 power 점수), 8개 도시 날씨/구장 정보
  - 탭 구조: 오늘 날씨 / 어제 경기 결과 / 마이팀(즐겨찾기 팀 승률·기사·일정)
  - 한글 초성 검색, 직관지수, 승리 확률 예측, 글래스모피즘 디자인, 배경 애니메이션
  - Weather Composition 실습(반응형 상태 + computed 필터링 + watch/watchEffect 감시)을 ChatGPT와 함께 이어서 반영
- src/assets/practice.css, src/assets/exercise.css: 실습/과제 공용 스타일
- src/assets/south-korea-map.svg: 지역 지도 관련 에셋

### 수정된 기존 파일

- src/App.vue: mode 토글("Vue 문법 실습" / "과제") 추가, Composition API 섹션 컴포넌트 전체 연결
- src/assets/main.css: Vite 기본 스캐폴딩의 `#app max-width`/grid 레이아웃 제거 → 전체 화면 폭 사용
- src/components/practices/basic/VueFor.vue, VueShow.vue: 세부 수정
