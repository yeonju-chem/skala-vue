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
