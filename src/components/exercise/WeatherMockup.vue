<script setup>
import { computed, onMounted, ref, watch, watchEffect } from 'vue'
import southKoreaMap from '@/assets/south-korea-map.svg'

// ================================================================
// Hands on - Weather Composition
// 작성자: 이연주 · SKALA
// ================================================================

// 과제 필수 반응형 상태: 검색어, 선택 도시, 지역별 날씨 데이터
const searchQuery = ref('')
const selectedCityInfo = ref('구장 조명을 선택하면 오늘의 직관 정보가 열립니다.')
const selectedCityId = ref('city_01')
const hoveredCityId = ref(null)
const showOnlyGames = ref(false) // 개인 추가 반응형 상태
const isSelectionLocked = ref(false)
const currentTab = ref('map')
const myTeam = ref('')

const weatherList = ref([
  {
    id: 'city_01',
    name: '서울',
    stadium: '잠실야구장',
    temp: 27,
    status: '맑음',
    rain: 5,
    x: 41,
    y: 22,
    icon: 'oval',
    game: { away: 'LG 트윈스', home: '두산 베어스', time: '18:30' },
  },
  {
    id: 'city_09',
    name: '서울',
    stadium: '고척 스카이돔',
    temp: 27,
    status: '맑음',
    rain: 5,
    x: 36.5,
    y: 24.5,
    icon: 'dome',
    game: null,
  },
  {
    id: 'city_02',
    name: '인천',
    stadium: 'SSG 랜더스필드',
    temp: 23,
    status: '흐림',
    rain: 35,
    x: 29,
    y: 27,
    icon: 'landing',
    game: { away: '롯데 자이언츠', home: 'SSG 랜더스', time: '18:30' },
  },
  {
    id: 'city_03',
    name: '수원',
    stadium: 'KT 위즈파크',
    temp: 21,
    status: '비',
    rain: 82,
    x: 42,
    y: 33,
    icon: 'roof',
    game: { away: 'NC 다이노스', home: 'KT 위즈', time: '18:30' },
  },
  {
    id: 'city_04',
    name: '대전',
    stadium: '한화생명 볼파크',
    temp: 29,
    status: '맑음',
    rain: 5,
    x: 45,
    y: 48,
    icon: 'bowl',
    game: { away: 'KIA 타이거즈', home: '한화 이글스', time: '18:30' },
  },
  {
    id: 'city_05',
    name: '광주',
    stadium: '기아 챔피언스필드',
    temp: 26,
    status: '구름',
    rain: 20,
    x: 33,
    y: 66,
    icon: 'wing',
    game: null,
  },
  {
    id: 'city_06',
    name: '대구',
    stadium: '삼성 라이온즈파크',
    temp: 30,
    status: '맑음',
    rain: 5,
    x: 67,
    y: 59,
    icon: 'diamond',
    game: { away: '키움 히어로즈', home: '삼성 라이온즈', time: '18:30' },
  },
  {
    id: 'city_07',
    name: '부산',
    stadium: '사직야구장',
    temp: 24,
    status: '흐림',
    rain: 38,
    x: 72,
    y: 75,
    icon: 'classic',
    game: null,
  },
  {
    id: 'city_08',
    name: '창원',
    stadium: '창원 NC파크',
    temp: 22,
    status: '비',
    rain: 85,
    x: 57,
    y: 73,
    icon: 'park',
    game: null,
  },
])

const yesterdayResults = ref([
  {
    id: 1,
    away: 'KIA',
    home: '두산',
    awayScore: 7,
    homeScore: 3,
    stadium: '잠실',
    note: '경기 종료',
  },
  { id: 2, away: 'LG', home: '키움', awayScore: 4, homeScore: 5, stadium: '고척', note: '끝내기' },
  {
    id: 3,
    away: '삼성',
    home: 'SSG',
    awayScore: 2,
    homeScore: 2,
    stadium: '문학',
    note: '연장 무승부',
  },
  {
    id: 4,
    away: 'KT',
    home: '롯데',
    awayScore: 1,
    homeScore: 8,
    stadium: '사직',
    note: '경기 종료',
  },
  {
    id: 5,
    away: '한화',
    home: 'NC',
    awayScore: 0,
    homeScore: 0,
    stadium: '창원',
    note: '우천 취소',
  },
])

const teamNames = [
  'LG 트윈스',
  '두산 베어스',
  '키움 히어로즈',
  'SSG 랜더스',
  'KT 위즈',
  '한화 이글스',
  'KIA 타이거즈',
  '삼성 라이온즈',
  '롯데 자이언츠',
  'NC 다이노스',
]
const teamColors = {
  'LG 트윈스': '#c30452',
  '두산 베어스': '#131230',
  '키움 히어로즈': '#570514',
  'SSG 랜더스': '#ce0e2d',
  'KT 위즈': '#202020',
  '한화 이글스': '#f36f21',
  'KIA 타이거즈': '#ea0029',
  '삼성 라이온즈': '#074ca1',
  '롯데 자이언츠': '#041e42',
  'NC 다이노스': '#315288',
}

onMounted(() => {
  myTeam.value = window.localStorage.getItem('ballpark-my-team') || ''
})

const CHO_LIST = [
  'ㄱ',
  'ㄲ',
  'ㄴ',
  'ㄷ',
  'ㄸ',
  'ㄹ',
  'ㅁ',
  'ㅂ',
  'ㅃ',
  'ㅅ',
  'ㅆ',
  'ㅇ',
  'ㅈ',
  'ㅉ',
  'ㅊ',
  'ㅋ',
  'ㅌ',
  'ㅍ',
  'ㅎ',
]

const getChosung = (text) =>
  [...text]
    .map((char) => {
      const code = char.charCodeAt(0) - 0xac00
      return code >= 0 && code <= 11171 ? CHO_LIST[Math.floor(code / 588)] : char
    })
    .join('')

// 과제 필수 computed: 도시 이름 또는 초성이 검색어를 포함하는 항목만 반환
const filteredWeatherList = computed(() => {
  const query = searchQuery.value.trim().toLowerCase()
  if (!query) return weatherList.value
  return weatherList.value.filter(
    (city) => city.name.toLowerCase().includes(query) || getChosung(city.name).includes(query),
  )
})

// 개인 추가 computed: 검색 결과에 경기 유무 필터까지 조합
const visibleWeatherList = computed(() =>
  showOnlyGames.value
    ? filteredWeatherList.value.filter((city) => city.game)
    : filteredWeatherList.value,
)

const selectedCity = computed(
  () => weatherList.value.find((city) => city.id === selectedCityId.value) ?? weatherList.value[0],
)

const hoveredCity = computed(() =>
  weatherList.value.find((city) => city.id === hoveredCityId.value),
)

const focusedCity = computed(() =>
  isSelectionLocked.value ? selectedCity.value : (hoveredCity.value ?? selectedCity.value),
)

const todayGameCount = computed(() => weatherList.value.filter((city) => city.game).length)
const safestCity = computed(
  () => [...weatherList.value].sort((a, b) => getWatchScore(b) - getWatchScore(a))[0],
)
const myTeamGame = computed(() =>
  weatherList.value.find(
    (city) => city.game && (city.game.home === myTeam.value || city.game.away === myTeam.value),
  ),
)

const getWatchScore = (city) => {
  const temperaturePenalty = Math.min(Math.abs(city.temp - 23) * 3, 30)
  const rainPenalty = city.rain * 0.45
  return Math.max(0, Math.min(100, Math.round(100 - temperaturePenalty - rainPenalty)))
}

const getWeatherIcon = (status) => ({ 맑음: '☀', 구름: '◒', 흐림: '☁', 비: '☂' })[status] ?? '◌'

const getMapLabel = (city) => {
  if (city.name !== '서울') return city.name
  return city.stadium.includes('고척') ? '고척' : '잠실'
}

const getTeamColor = (team) => teamColors[team] ?? '#ff9a72'

const getScoreLabel = (score) => {
  if (score >= 85) return '직관 최적'
  if (score >= 70) return '직관 추천'
  if (score >= 50) return '주의 필요'
  return '집관 추천'
}

const isVisibleCity = (cityId) => visibleWeatherList.value.some((city) => city.id === cityId)

const selectCity = (city) => {
  selectedCityId.value = city.id
  isSelectionLocked.value = true
  selectedCityInfo.value = `${city.name} ${city.stadium}이 선택되었습니다.`
}

const unlockSelection = () => {
  isSelectionLocked.value = false
  selectedCityInfo.value = '선택 고정이 해제되었습니다. 구장 위에 마우스를 올려보세요.'
}

const selectMyTeam = (team) => {
  myTeam.value = myTeam.value === team ? '' : team
}

const showDetail = (city) => {
  const gameInfo = city.game
    ? `${city.game.away} vs ${city.game.home} · ${city.game.time}`
    : '오늘 예정된 경기가 없습니다.'
  window.alert(
    `${city.name} · ${city.stadium}\n${gameInfo}\n${city.temp}°C · ${city.status} · 강수 ${city.rain}%\n직관지수 ${getWatchScore(city)}점`,
  )
}

// 과제 필수 watch: 선택에 따라 상태바 문구가 바뀌는 순간 기록
watch(selectedCityInfo, (newMessage, oldMessage) => {
  console.log(`[watch 감지] 상태바: "${oldMessage}" → "${newMessage}"`)
})

// 과제 필수 watchEffect: 타이핑할 때 검색어와 매칭 도시를 자동 추적
watchEffect(() => {
  console.log(
    `[watchEffect 자동 호출] 검색어 "${searchQuery.value}" 매칭 도시:`,
    filteredWeatherList.value.map((city) => city.name),
  )
})

// 개인 추가 watcher: 경기 필터 상태 추적
watch(showOnlyGames, (enabled) => {
  console.log(`[watch 감지] 경기 있는 구장만 보기: ${enabled}`)
})

watch(myTeam, (team) => {
  if (team) window.localStorage.setItem('ballpark-my-team', team)
  else window.localStorage.removeItem('ballpark-my-team')
})
</script>

<template>
  <main class="ballpark-app">
    <div class="night-sky" aria-hidden="true">
      <i v-for="number in 18" :key="number" :class="`star star-${number}`"></i>
      <div class="sky-glow glow-one"></div>
      <div class="sky-glow glow-two"></div>
      <div class="baseball-moon">
        <span class="seam seam-left"></span>
        <span class="seam seam-right"></span>
      </div>
      <div class="baseball-flight"><i>⚾</i></div>
      <div class="field-diamond"><i></i><i></i><i></i></div>
    </div>

    <header class="topbar">
      <a class="brand" href="#" aria-label="Ballpark 18시 30분 홈">
        <span class="brand-ball" aria-hidden="true">
          <i></i>
          <i></i>
        </span>
        <span><strong>BALLPARK</strong><small>18:30</small></span>
      </a>
      <div class="live-badge"><i></i> SUNSET FIRST PITCH · 5 GAMES</div>
    </header>

    <nav class="page-tabs" aria-label="야구 정보 메뉴">
      <button :class="{ active: currentTab === 'map' }" @click="currentTab = 'map'">
        <span>⌖</span> 오늘의 구장 지도
      </button>
      <button :class="{ active: currentTab === 'results' }" @click="currentTab = 'results'">
        <span>▦</span> 어제 경기
      </button>
      <button :class="{ active: currentTab === 'myteam' }" @click="currentTab = 'myteam'">
        <span>★</span> 마이팀
      </button>
    </nav>

    <section v-if="currentTab === 'map'" class="experience-layout">
      <aside class="intro-panel">
        <p class="section-label">PLAY BALL · PICK YOUR PARK</p>
        <h1>오늘의<br /><em>승리요정</em>은?</h1>
        <p class="intro-copy">
          응원할 구장을 콕 찍어보세요.<br />경기와 날씨 체크는 우리가 할게요.
        </p>

        <label class="search-field">
          <span aria-hidden="true">⌕</span>
          <input v-model="searchQuery" type="search" placeholder="도시 검색 · 초성도 가능" />
          <kbd>⌘ K</kbd>
        </label>

        <button
          class="game-filter"
          :class="{ active: showOnlyGames }"
          :aria-pressed="showOnlyGames"
          @click="showOnlyGames = !showOnlyGames"
        >
          <span class="toggle"><i></i></span>
          경기 있는 구장만 밝히기
        </button>

        <div v-if="visibleWeatherList.length" class="search-results">
          <button
            v-for="city in visibleWeatherList"
            :key="city.id"
            :class="{ active: selectedCityId === city.id }"
            @mouseenter="hoveredCityId = city.id"
            @mouseleave="hoveredCityId = null"
            @click="selectCity(city)"
          >
            <span>{{ city.name }}</span>
            <small>{{ city.stadium }}</small>
            <b>{{ city.temp }}°</b>
          </button>
        </div>
        <p v-else class="empty-result">검색 결과와 일치하는 도시가 없습니다.</p>
      </aside>

      <section class="map-section" aria-label="대한민국 야구장 지도">
        <div class="map-orbit orbit-one"></div>
        <div class="map-orbit orbit-two"></div>
        <div class="map-stage">
          <!-- Public domain: Wikimedia Commons, Map of South Korea-blank.svg -->
          <div class="map-halo" aria-hidden="true"></div>
          <img class="korea-map" :src="southKoreaMap" alt="" aria-hidden="true" />

          <button
            v-for="city in weatherList"
            :key="city.id"
            class="stadium-marker"
            :class="{
              selected: selectedCityId === city.id,
              dimmed: !isVisibleCity(city.id),
              rainy: city.status === '비',
            }"
            :style="{ left: city.x + '%', top: city.y + '%' }"
            :aria-label="`${city.name} ${city.stadium}`"
            @mouseenter="hoveredCityId = city.id"
            @mouseleave="hoveredCityId = null"
            @focus="hoveredCityId = city.id"
            @blur="hoveredCityId = null"
            @click="selectCity(city)"
          >
            <span class="light-beam"></span>
            <span class="stadium-icon">
              <svg viewBox="0 0 32 32" aria-hidden="true">
                <template v-if="city.icon === 'dome'">
                  <ellipse cx="16" cy="16" rx="14" ry="10.5" />
                  <path d="M4 12c7 3 17 3 24 0M4 20c7-3 17-3 24 0M16 6v20" />
                </template>
                <template v-else-if="city.icon === 'oval'">
                  <ellipse cx="16" cy="16" rx="13.5" ry="11.5" />
                  <path d="M6 21c2-9 18-9 20 0M16 11l5 5-5 5-5-5Z" />
                </template>
                <template v-else-if="city.icon === 'roof'">
                  <path d="M4 23C1 13 6 4 16 3c10 1 15 10 12 20l-6-3H10Z" />
                  <path d="M16 10l5 5-5 5-5-5m-6-9 4 3m18-3-4 3" />
                </template>
                <template v-else-if="city.icon === 'wing'">
                  <path d="M2 24C1 12 6 4 16 3c10 1 15 9 14 21l-7-3H9Z" />
                  <path d="M2 9h6M30 9h-6M16 10l5 5-5 5-5-5" />
                </template>
                <template v-else-if="city.icon === 'diamond'">
                  <path d="M16 2 27 6l4 11-7 13H8L1 17 5 6Z" />
                  <path d="m16 9 6 6-6 6-6-6Zm0 12v6" />
                </template>
                <template v-else-if="city.icon === 'classic'">
                  <ellipse cx="16" cy="16" rx="13" ry="12" />
                  <path d="M5 20c3-9 19-9 22 0M16 10l5 5-5 5-5-5Z" />
                </template>
                <template v-else-if="city.icon === 'landing'">
                  <path d="M3 24C1 13 5 4 16 3c11 1 15 10 13 21l-7-3H10Z" />
                  <path d="M5 8h6m10 0h6M16 10l5 5-5 5-5-5" />
                </template>
                <template v-else-if="city.icon === 'bowl'">
                  <path d="M3 25C1 13 5 5 14 3h5c9 3 13 12 9 22l-6-4H10Z" />
                  <path d="M16 10l5 5-5 5-5-5M4 13l5-2m19 2-5-2" />
                </template>
                <template v-else>
                  <path d="M3 24 5 8l7-5h8l7 5 2 16-7-3H10Z" />
                  <path d="M16 10l5 5-5 5-5-5M7 7l3 3m15-3-3 3" />
                </template>
              </svg>
            </span>
            <b>{{ getMapLabel(city) }}</b>

            <span v-if="hoveredCityId === city.id" class="map-tooltip">
              <small>{{ city.stadium }}</small>
              <strong v-if="city.game">{{ city.game.away }} <i>VS</i> {{ city.game.home }}</strong>
              <strong v-else>오늘 경기 없음</strong>
              <span>
                {{ getWeatherIcon(city.status) }} {{ city.temp }}° · 강수 {{ city.rain }}%
                <em>직관 {{ getWatchScore(city) }}</em>
              </span>
            </span>
          </button>

          <div class="map-caption"><i></i> 구장에 마우스를 올려보세요</div>
        </div>
      </section>

      <aside class="detail-panel">
        <div class="detail-topline">
          <span>{{ focusedCity.name }}</span>
          <div>
            <button v-if="isSelectionLocked" @click="unlockSelection">고정 해제</button>
            <b>{{ focusedCity.game ? 'GAME ON' : 'NO GAME' }}</b>
          </div>
        </div>
        <p class="stadium-title">{{ focusedCity.stadium }}</p>

        <div v-if="focusedCity.game" class="matchup">
          <div>
            <small>AWAY</small><strong>{{ focusedCity.game.away }}</strong>
          </div>
          <p>
            <b>{{ focusedCity.game.time }}</b
            ><span>PLAY BALL</span>
          </p>
          <div class="home">
            <small>HOME</small><strong>{{ focusedCity.game.home }}</strong>
          </div>
        </div>
        <div v-else class="off-day">오늘은 구장이 잠시 쉬어갑니다.</div>

        <div class="weather-dial">
          <div class="dial-ring" :style="{ '--score': getWatchScore(focusedCity) * 3.6 + 'deg' }">
            <div>
              <strong>{{ getWatchScore(focusedCity) }}</strong
              ><span>직관지수</span>
            </div>
          </div>
          <div class="weather-main">
            <span>{{ getWeatherIcon(focusedCity.status) }}</span>
            <p>
              <strong>{{ focusedCity.temp }}°</strong><small>{{ focusedCity.status }}</small>
            </p>
          </div>
        </div>

        <div class="weather-stats">
          <div>
            <span>강수확률</span><strong>{{ focusedCity.rain }}%</strong>
          </div>
          <div>
            <span>추천</span><strong>{{ getScoreLabel(getWatchScore(focusedCity)) }}</strong>
          </div>
        </div>

        <button class="detail-button" @click.stop="showDetail(focusedCity)">
          경기·날씨 상세보기 <span>↗</span>
        </button>
      </aside>
    </section>

    <section v-else-if="currentTab === 'results'" class="subpage results-page">
      <header class="subpage-header">
        <p>YESTERDAY · FINAL SCORES</p>
        <h1>어제의 함성, <em>스코어로 다시 보기</em></h1>
        <span>끝내기부터 우천 취소까지, 어제 경기를 한눈에 확인하세요.</span>
      </header>
      <div class="result-grid">
        <article v-for="game in yesterdayResults" :key="game.id" class="result-game">
          <div class="result-meta">
            <span>{{ game.stadium }}</span
            ><b>{{ game.note }}</b>
          </div>
          <div class="result-score">
            <p>
              <small>AWAY</small><strong>{{ game.away }}</strong>
            </p>
            <div>
              <b>{{ game.awayScore }}</b
              ><i>:</i><b>{{ game.homeScore }}</b>
            </div>
            <p class="home">
              <small>HOME</small><strong>{{ game.home }}</strong>
            </p>
          </div>
          <div class="inning-line">
            <i></i><i></i><i></i><i></i><i></i><i></i><i></i><i></i><i></i>
          </div>
        </article>
      </div>
    </section>

    <section v-else class="subpage myteam-page">
      <header class="subpage-header">
        <p>MY TEAM · MY BALLPARK</p>
        <h1>응원팀 하나면, <em>첫 화면부터 내 경기</em></h1>
        <span>응원하는 팀을 선택하면 오늘 경기와 구장 날씨를 먼저 보여드려요.</span>
      </header>
      <div class="team-selector">
        <button
          v-for="team in teamNames"
          :key="team"
          :class="{ active: myTeam === team }"
          :style="{ '--team-color': getTeamColor(team) }"
          @click="selectMyTeam(team)"
        >
          <span class="team-ball">⚾</span>{{ team }}
        </button>
      </div>
      <div v-if="myTeam" class="myteam-hero" :style="{ '--team-color': getTeamColor(myTeam) }">
        <div class="team-monogram">{{ myTeam.slice(0, 2) }}</div>
        <div class="myteam-copy">
          <small>나의 응원팀</small>
          <h2>{{ myTeam }}</h2>
          <p v-if="myTeamGame">
            오늘 {{ myTeamGame.game.time }} · {{ myTeamGame.stadium }}에서 경기가 있어요.
          </p>
          <p v-else>오늘은 경기가 없습니다. 다음 승리를 위해 잠시 충전 중!</p>
        </div>
        <div v-if="myTeamGame" class="myteam-weather">
          <span>{{ getWeatherIcon(myTeamGame.status) }}</span>
          <strong>{{ myTeamGame.temp }}°</strong>
          <small>직관지수 {{ getWatchScore(myTeamGame) }}</small>
        </div>
      </div>
      <p v-else class="team-empty">야구공을 하나 골라 나만의 홈 화면을 완성해 보세요.</p>
    </section>

    <footer v-if="currentTab === 'map'" class="status-dock">
      <span class="status-ball">●</span>
      <p>{{ selectedCityInfo }}</p>
      <div>
        <b>{{ todayGameCount }}</b> TODAY GAMES
      </div>
      <div>
        <b>{{ safestCity.name }}</b> BEST WEATHER
      </div>
    </footer>
  </main>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;500;600;700&family=Noto+Sans+KR:wght@400;500;600;700;800&display=swap');

.ballpark-app {
  --night: #19273b;
  --panel: rgba(26, 34, 54, 0.7);
  --line: rgba(255, 224, 205, 0.17);
  --mint: #ffe4b8;
  --orange: #ff9a72;
  position: relative;
  min-height: 100vh;
  overflow: hidden;
  padding: 28px 38px 26px;
  color: #edf8f8;
  background: var(--night);
  font-family: 'DM Sans', 'Noto Sans KR', sans-serif;
}
.ballpark-app * {
  box-sizing: border-box;
}
button,
input {
  font: inherit;
}

.night-sky {
  position: fixed;
  inset: 0;
  overflow: hidden;
  pointer-events: none;
  background:
    radial-gradient(circle at 53% 66%, rgba(255, 170, 92, 0.78), transparent 23%),
    radial-gradient(circle at 72% 55%, rgba(232, 109, 102, 0.5), transparent 37%),
    linear-gradient(180deg, #2e405b 0%, #6b5064 38%, #c76f62 65%, #eea06e 80%, #223249 100%);
}
.night-sky::before {
  content: '';
  position: absolute;
  left: 52%;
  top: 64%;
  width: 150px;
  height: 150px;
  border-radius: 50%;
  background: #ffd49a;
  box-shadow: 0 0 80px 35px rgba(255, 180, 112, 0.28);
  transform: translate(-50%, -50%);
  opacity: 0.52;
  animation: sunset-pulse 6s ease-in-out infinite alternate;
}
@keyframes sunset-pulse {
  to {
    opacity: 0.72;
    box-shadow: 0 0 110px 48px rgba(255, 180, 112, 0.32);
  }
}
.night-sky::after {
  content: '';
  position: absolute;
  inset: 0;
  opacity: 0.08;
  background-image:
    linear-gradient(rgba(255, 255, 255, 0.025) 1px, transparent 1px),
    linear-gradient(90deg, rgba(255, 255, 255, 0.025) 1px, transparent 1px);
  background-size: 44px 44px;
  mask-image: radial-gradient(circle, #000, transparent 75%);
}
.star {
  position: absolute;
  width: 2px;
  height: 2px;
  border-radius: 50%;
  background: #d9ffff;
  opacity: 0.32;
  animation: twinkle 3s ease-in-out infinite alternate;
}
.star-1 {
  left: 7%;
  top: 15%;
}
.star-2 {
  left: 16%;
  top: 72%;
  animation-delay: -1s;
}
.star-3 {
  left: 24%;
  top: 8%;
}
.star-4 {
  left: 31%;
  top: 43%;
  animation-delay: -2s;
}
.star-5 {
  left: 39%;
  top: 77%;
}
.star-6 {
  left: 47%;
  top: 14%;
  animation-delay: -1.5s;
}
.star-7 {
  left: 53%;
  top: 59%;
}
.star-8 {
  left: 62%;
  top: 6%;
}
.star-9 {
  left: 69%;
  top: 38%;
  animation-delay: -0.7s;
}
.star-10 {
  left: 76%;
  top: 81%;
}
.star-11 {
  left: 83%;
  top: 17%;
}
.star-12 {
  left: 91%;
  top: 61%;
  animation-delay: -2s;
}
.star-13 {
  left: 12%;
  top: 39%;
}
.star-14 {
  left: 35%;
  top: 22%;
}
.star-15 {
  left: 58%;
  top: 88%;
}
.star-16 {
  left: 73%;
  top: 13%;
}
.star-17 {
  left: 87%;
  top: 43%;
}
.star-18 {
  left: 95%;
  top: 25%;
}
@keyframes twinkle {
  to {
    opacity: 0.95;
    transform: scale(2);
  }
}
.sky-glow {
  position: absolute;
  border-radius: 50%;
  filter: blur(90px);
  opacity: 0.14;
  animation: drift 14s ease-in-out infinite alternate;
}
.glow-one {
  width: 42vw;
  height: 42vw;
  left: 20%;
  top: -25%;
  background: #ffb06d;
}
.glow-two {
  width: 36vw;
  height: 36vw;
  right: -10%;
  bottom: -20%;
  background: #a65e9d;
  animation-delay: -6s;
}
@keyframes drift {
  to {
    transform: translate(120px, 90px) scale(1.2);
  }
}

.baseball-moon {
  position: absolute;
  right: 7vw;
  top: 9vh;
  width: 130px;
  height: 130px;
  border-radius: 50%;
  opacity: 0.09;
  border: 1px solid #fff;
  animation: moon-spin 28s linear infinite;
}
.baseball-flight {
  position: absolute;
  top: 20%;
  left: 18%;
  width: 38vw;
  height: 23vh;
  border-top: 1px dashed rgba(255, 235, 210, 0.22);
  border-radius: 50%;
  transform: rotate(-8deg);
}
.baseball-flight i {
  position: absolute;
  top: -11px;
  left: 0;
  font-size: 18px;
  filter: sepia(0.25);
  animation: ball-flight 8s ease-in-out infinite;
}
@keyframes ball-flight {
  50% {
    left: 100%;
    transform: rotate(540deg) scale(0.7);
  }
  100% {
    left: 0;
    transform: rotate(1080deg);
  }
}
.field-diamond {
  position: absolute;
  right: 10%;
  bottom: -17vw;
  width: 38vw;
  height: 38vw;
  border: 1px solid rgba(255, 231, 201, 0.09);
  transform: rotate(45deg);
}
.field-diamond i {
  position: absolute;
  width: 8px;
  height: 8px;
  border: 1px solid rgba(255, 239, 219, 0.28);
  background: rgba(255, 190, 129, 0.14);
}
.field-diamond i:nth-child(1) {
  left: -4px;
  top: -4px;
}
.field-diamond i:nth-child(2) {
  right: -4px;
  top: -4px;
}
.field-diamond i:nth-child(3) {
  left: -4px;
  bottom: -4px;
}
.seam {
  position: absolute;
  top: 14px;
  bottom: 14px;
  width: 45px;
  border: 2px dashed #fff;
  border-top: 0;
  border-bottom: 0;
  border-radius: 50%;
}
.seam-left {
  left: 20px;
  transform: rotate(-16deg);
}
.seam-right {
  right: 20px;
  transform: rotate(16deg);
}
@keyframes moon-spin {
  to {
    transform: rotate(360deg);
  }
}

.topbar {
  position: relative;
  z-index: 5;
  display: flex;
  align-items: center;
  justify-content: space-between;
  max-width: 1500px;
  margin: 0 auto;
}
.brand {
  display: inline-flex;
  align-items: center;
  gap: 11px;
  color: #fff;
  text-decoration: none;
}
.brand-ball {
  position: relative;
  display: block;
  width: 38px;
  height: 38px;
  overflow: hidden;
  border-radius: 50%;
  background: #f5fffb;
  box-shadow: 0 0 28px rgba(144, 243, 206, 0.25);
  animation: logo-roll 9s linear infinite;
}
.brand-ball i {
  position: absolute;
  top: -3px;
  width: 19px;
  height: 44px;
  border: 2px dashed #ed6d62;
  border-top: 0;
  border-bottom: 0;
  border-radius: 50%;
}
.brand-ball i:first-child {
  left: 4px;
  transform: rotate(-12deg);
}
.brand-ball i:last-child {
  right: 4px;
  transform: rotate(12deg);
}
@keyframes logo-roll {
  to {
    transform: rotate(360deg);
  }
}
.brand > span:last-child {
  display: flex;
  flex-direction: column;
  line-height: 1;
}
.brand strong {
  font-size: 14px;
  letter-spacing: 2px;
}
.brand small {
  margin-top: 5px;
  color: var(--mint);
  font-size: 8px;
  letter-spacing: 4px;
}
.live-badge {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 13px;
  border: 1px solid var(--line);
  border-radius: 99px;
  color: rgba(235, 251, 250, 0.66);
  background: rgba(4, 16, 27, 0.45);
  font-size: 9px;
  letter-spacing: 1.5px;
}
.live-badge i {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: #ff796c;
  box-shadow: 0 0 0 5px rgba(255, 121, 108, 0.1);
  animation: live 1.6s ease-in-out infinite;
}

.page-tabs {
  position: relative;
  z-index: 8;
  display: flex;
  justify-content: center;
  gap: 5px;
  width: fit-content;
  margin: 9px auto -40px;
  padding: 4px;
  border: 1px solid var(--line);
  border-radius: 999px;
  background: rgba(30, 35, 54, 0.5);
  backdrop-filter: blur(18px);
}
.page-tabs button {
  display: flex;
  align-items: center;
  gap: 7px;
  padding: 8px 14px;
  border: 0;
  border-radius: 999px;
  color: rgba(255, 247, 238, 0.55);
  background: transparent;
  font-size: 9px;
  cursor: pointer;
  transition: 0.2s ease;
}
.page-tabs button span {
  color: var(--orange);
  font-size: 12px;
}
.page-tabs button.active {
  color: #39251f;
  background: #ffe3bf;
  box-shadow: 0 7px 24px rgba(73, 35, 29, 0.2);
}
.page-tabs button.active span {
  color: #c45d45;
}
@keyframes live {
  50% {
    box-shadow: 0 0 0 9px transparent;
  }
}

.experience-layout {
  position: relative;
  z-index: 2;
  display: grid;
  grid-template-columns: minmax(250px, 0.7fr) minmax(440px, 1.5fr) minmax(270px, 0.78fr);
  align-items: center;
  gap: 22px;
  max-width: 1500px;
  min-height: calc(100vh - 150px);
  margin: 6px auto 0;
}
.intro-panel {
  align-self: center;
  padding: 30px 0;
}
.section-label {
  margin: 0 0 14px;
  color: var(--mint);
  font-size: 9px;
  font-weight: 700;
  letter-spacing: 2.2px;
}
.intro-panel h1 {
  margin: 0;
  color: #f4fbfb;
  font-family: 'Noto Sans KR', sans-serif;
  font-size: clamp(38px, 4vw, 62px);
  font-weight: 800;
  line-height: 1.08;
  letter-spacing: -3px;
}
.intro-panel h1 em {
  color: transparent;
  font-style: normal;
  -webkit-text-stroke: 1px rgba(144, 243, 206, 0.9);
  text-shadow: 0 0 35px rgba(144, 243, 206, 0.15);
}
.intro-copy {
  margin: 20px 0 28px;
  color: rgba(229, 245, 246, 0.52);
  font-size: 12px;
  line-height: 1.8;
}
.search-field {
  display: flex;
  align-items: center;
  gap: 9px;
  max-width: 320px;
  padding: 11px 13px;
  border: 1px solid var(--line);
  border-radius: 13px;
  background: rgba(10, 31, 47, 0.58);
  backdrop-filter: blur(15px);
  transition:
    border-color 0.2s,
    box-shadow 0.2s;
}
.search-field:focus-within {
  border-color: rgba(144, 243, 206, 0.5);
  box-shadow: 0 0 0 4px rgba(144, 243, 206, 0.05);
}
.search-field > span {
  color: var(--mint);
  font-size: 21px;
}
.search-field input {
  min-width: 0;
  flex: 1;
  border: 0;
  outline: 0;
  color: #fff;
  background: transparent;
  font-size: 11px;
}
.search-field input::placeholder {
  color: rgba(255, 255, 255, 0.35);
}
.search-field kbd {
  padding: 3px 5px;
  border: 1px solid var(--line);
  border-radius: 5px;
  color: rgba(255, 255, 255, 0.3);
  font-size: 8px;
}
.game-filter {
  display: flex;
  align-items: center;
  gap: 9px;
  margin: 13px 0 20px;
  padding: 0;
  border: 0;
  color: rgba(255, 255, 255, 0.45);
  background: transparent;
  font-size: 10px;
  cursor: pointer;
}
.toggle {
  position: relative;
  width: 26px;
  height: 14px;
  border-radius: 99px;
  background: rgba(255, 255, 255, 0.14);
  transition: 0.2s;
}
.toggle i {
  position: absolute;
  top: 3px;
  left: 3px;
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.55);
  transition: 0.2s;
}
.game-filter.active {
  color: var(--mint);
}
.game-filter.active .toggle {
  background: rgba(144, 243, 206, 0.25);
}
.game-filter.active .toggle i {
  left: 15px;
  background: var(--mint);
}
.search-results {
  display: grid;
  gap: 4px;
  max-width: 320px;
  max-height: 245px;
  overflow: auto;
  padding-right: 5px;
}
.search-results::-webkit-scrollbar {
  width: 3px;
}
.search-results::-webkit-scrollbar-thumb {
  background: rgba(144, 243, 206, 0.2);
  border-radius: 9px;
}
.search-results button {
  display: grid;
  grid-template-columns: 42px 1fr auto;
  align-items: center;
  gap: 8px;
  width: 100%;
  padding: 8px 10px;
  border: 1px solid transparent;
  border-radius: 10px;
  color: rgba(255, 255, 255, 0.5);
  background: transparent;
  text-align: left;
  cursor: pointer;
  transition: 0.18s;
}
.search-results button:hover,
.search-results button.active {
  border-color: var(--line);
  color: #fff;
  background: rgba(255, 255, 255, 0.045);
}
.search-results span {
  font-size: 11px;
  font-weight: 700;
}
.search-results small {
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
  font-size: 8px;
  opacity: 0.55;
}
.search-results b {
  color: var(--mint);
  font-size: 11px;
}
.empty-result {
  max-width: 300px;
  padding: 16px;
  border: 1px dashed var(--line);
  border-radius: 12px;
  color: rgba(255, 255, 255, 0.4);
  font-size: 10px;
  text-align: center;
}

.map-section {
  position: relative;
  display: grid;
  place-items: center;
  min-height: 700px;
}
.map-stage {
  position: relative;
  width: min(100%, 650px);
  height: 700px;
}
.korea-map {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: contain;
  opacity: 0.42;
  filter: sepia(0.8) saturate(0.8) hue-rotate(335deg) brightness(0.78)
    drop-shadow(0 25px 38px rgba(23, 16, 35, 0.42));
}
.map-halo {
  position: absolute;
  inset: 7% 18%;
  border-radius: 44%;
  background: rgba(255, 183, 142, 0.14);
  filter: blur(42px);
  animation: map-breathe 5s ease-in-out infinite alternate;
}
@keyframes map-breathe {
  to {
    opacity: 0.5;
    transform: scale(1.06);
  }
}
.map-orbit {
  position: absolute;
  border: 1px solid rgba(120, 220, 218, 0.055);
  border-radius: 50%;
  pointer-events: none;
}
.orbit-one {
  width: 620px;
  height: 620px;
  animation: orbit 32s linear infinite;
}
.orbit-two {
  width: 760px;
  height: 760px;
  border-style: dashed;
  animation: orbit 50s linear infinite reverse;
}
@keyframes orbit {
  to {
    transform: rotate(360deg);
  }
}
.stadium-marker {
  position: absolute;
  z-index: 4;
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 58px;
  padding: 0;
  border: 0;
  color: #eafff8;
  background: transparent;
  transform: translate(-50%, -50%);
  cursor: pointer;
  transition:
    opacity 0.25s,
    filter 0.25s;
}
.stadium-marker.dimmed {
  opacity: 0.08;
  filter: grayscale(1);
  pointer-events: none;
}
.stadium-icon {
  position: relative;
  z-index: 3;
  display: grid;
  place-items: center;
  width: 37px;
  height: 34px;
  border: 1px solid rgba(144, 243, 206, 0.72);
  border-radius: 12px 12px 16px 16px;
  background: #0a2637;
  box-shadow:
    0 0 15px rgba(144, 243, 206, 0.38),
    0 0 35px rgba(144, 243, 206, 0.12);
  transition: 0.22s;
}
.stadium-icon svg {
  width: 23px;
  height: 23px;
  overflow: visible;
  fill: none;
  stroke: currentColor;
  stroke-width: 1.35;
  stroke-linecap: round;
  stroke-linejoin: round;
}
.stadium-marker:hover .stadium-icon,
.stadium-marker:focus .stadium-icon,
.stadium-marker.selected .stadium-icon {
  transform: scale(1.25);
  background: var(--mint);
  box-shadow:
    0 0 24px rgba(144, 243, 206, 0.8),
    0 0 55px rgba(144, 243, 206, 0.28);
}
.stadium-marker.rainy .stadium-icon {
  border-color: #73bfff;
  box-shadow: 0 0 18px rgba(75, 163, 255, 0.45);
}
.stadium-marker:hover .stadium-icon svg,
.stadium-marker:focus .stadium-icon svg,
.stadium-marker.selected .stadium-icon svg {
  color: #092132;
}
.stadium-marker > b {
  margin-top: 6px;
  font-size: 9px;
  text-shadow: 0 2px 8px #000;
}
.light-beam {
  position: absolute;
  bottom: 38px;
  width: 36px;
  height: 110px;
  opacity: 0.09;
  background: linear-gradient(to top, var(--mint), transparent);
  clip-path: polygon(40% 100%, 60% 100%, 100% 0, 0 0);
  transform-origin: bottom;
  animation: beam 4s ease-in-out infinite alternate;
  pointer-events: none;
}
@keyframes beam {
  to {
    opacity: 0.2;
    transform: rotate(7deg);
  }
}
.map-tooltip {
  position: absolute;
  z-index: 20;
  left: 55px;
  top: -25px;
  display: flex;
  flex-direction: column;
  width: 205px;
  padding: 13px 15px;
  border: 1px solid rgba(144, 243, 206, 0.22);
  border-radius: 13px;
  color: #fff;
  background: rgba(5, 20, 31, 0.94);
  box-shadow: 0 18px 38px rgba(0, 0, 0, 0.38);
  backdrop-filter: blur(16px);
  text-align: left;
  pointer-events: none;
  animation: tooltip-in 0.18s ease-out;
}
.stadium-marker:nth-child(n + 7) .map-tooltip {
  right: 55px;
  left: auto;
}
@keyframes tooltip-in {
  from {
    opacity: 0;
    transform: translateY(8px);
  }
}
.map-tooltip small {
  color: var(--mint);
  font-size: 8px;
  letter-spacing: 0.4px;
}
.map-tooltip > strong {
  margin: 7px 0;
  font-size: 10px;
}
.map-tooltip > strong i {
  margin: 0 3px;
  color: var(--orange);
  font-size: 7px;
  font-style: normal;
}
.map-tooltip > span {
  display: flex;
  align-items: center;
  gap: 5px;
  color: rgba(255, 255, 255, 0.58);
  font-size: 9px;
}
.map-tooltip em {
  margin-left: auto;
  padding: 3px 6px;
  border-radius: 99px;
  color: #09291f;
  background: var(--mint);
  font-size: 8px;
  font-style: normal;
  font-weight: 800;
}
.map-caption {
  position: absolute;
  right: 8%;
  bottom: 4%;
  display: flex;
  align-items: center;
  gap: 7px;
  color: rgba(255, 255, 255, 0.3);
  font-size: 8px;
  letter-spacing: 0.5px;
}
.map-caption i {
  width: 5px;
  height: 5px;
  border-radius: 50%;
  background: var(--mint);
  box-shadow: 0 0 8px var(--mint);
}

.detail-panel {
  padding: 22px;
  border: 1px solid var(--line);
  border-radius: 22px;
  background: linear-gradient(145deg, rgba(13, 39, 57, 0.74), rgba(7, 23, 36, 0.7));
  box-shadow: 0 25px 65px rgba(0, 0, 0, 0.22);
  backdrop-filter: blur(20px);
}
.detail-topline {
  display: flex;
  align-items: center;
  justify-content: space-between;
  color: var(--mint);
  font-size: 9px;
  font-weight: 700;
  letter-spacing: 1.2px;
}
.detail-topline > div {
  display: flex;
  align-items: center;
  gap: 6px;
}
.detail-topline button {
  padding: 4px 7px;
  border: 1px solid rgba(255, 226, 190, 0.25);
  border-radius: 999px;
  color: rgba(255, 238, 216, 0.7);
  background: transparent;
  font-size: 7px;
  cursor: pointer;
}
.detail-topline b {
  padding: 4px 7px;
  border-radius: 99px;
  color: #142519;
  background: var(--mint);
  font-size: 7px;
}
.stadium-title {
  margin: 8px 0 18px;
  color: #fff;
  font-size: 18px;
  font-weight: 700;
  letter-spacing: -0.6px;
}
.matchup {
  display: grid;
  grid-template-columns: 1fr auto 1fr;
  align-items: center;
  gap: 8px;
  padding: 15px 0;
  border-top: 1px solid var(--line);
  border-bottom: 1px solid var(--line);
}
.matchup > div {
  display: flex;
  flex-direction: column;
}
.matchup .home {
  text-align: right;
}
.matchup small {
  color: rgba(255, 255, 255, 0.3);
  font-size: 7px;
  letter-spacing: 1px;
}
.matchup strong {
  margin-top: 4px;
  font-size: 10px;
}
.matchup p {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 0;
}
.matchup p b {
  font-size: 18px;
}
.matchup p span {
  color: var(--orange);
  font-size: 6px;
  letter-spacing: 1px;
}
.off-day {
  padding: 24px 0;
  border-top: 1px solid var(--line);
  border-bottom: 1px solid var(--line);
  color: rgba(255, 255, 255, 0.4);
  font-size: 10px;
  text-align: center;
}
.weather-dial {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 24px 4px 18px;
}
.dial-ring {
  display: grid;
  place-items: center;
  width: 104px;
  height: 104px;
  border-radius: 50%;
  background: conic-gradient(var(--mint) var(--score), rgba(255, 255, 255, 0.08) 0);
  box-shadow: 0 0 28px rgba(144, 243, 206, 0.08);
}
.dial-ring::before {
  content: '';
  position: absolute;
  width: 88px;
  height: 88px;
  border-radius: 50%;
  background: #0b2537;
}
.dial-ring > div {
  position: relative;
  z-index: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.dial-ring strong {
  font-size: 28px;
}
.dial-ring span {
  color: rgba(255, 255, 255, 0.4);
  font-size: 7px;
}
.weather-main {
  display: flex;
  align-items: center;
  gap: 8px;
}
.weather-main > span {
  color: #ffcb75;
  font-size: 27px;
}
.weather-main p {
  display: flex;
  flex-direction: column;
  margin: 0;
}
.weather-main strong {
  font-size: 30px;
  font-weight: 500;
}
.weather-main small {
  color: rgba(255, 255, 255, 0.42);
  font-size: 8px;
}
.weather-stats {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 7px;
}
.weather-stats div {
  display: flex;
  flex-direction: column;
  padding: 10px;
  border-radius: 10px;
  background: rgba(255, 255, 255, 0.035);
}
.weather-stats span {
  color: rgba(255, 255, 255, 0.35);
  font-size: 7px;
}
.weather-stats strong {
  margin-top: 3px;
  font-size: 9px;
}
.detail-button {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 100%;
  margin-top: 14px;
  padding: 11px 13px;
  border: 1px solid rgba(144, 243, 206, 0.25);
  border-radius: 11px;
  color: var(--mint);
  background: rgba(144, 243, 206, 0.06);
  font-size: 9px;
  cursor: pointer;
  transition: 0.2s;
}
.detail-button:hover {
  color: #09251c;
  background: var(--mint);
}
.detail-button span {
  font-size: 13px;
}

/* 결과 / 마이팀 서브페이지 */
.subpage {
  position: relative;
  z-index: 3;
  max-width: 1180px;
  min-height: calc(100vh - 125px);
  margin: 0 auto;
  padding: 115px 20px 70px;
}
.subpage-header {
  max-width: 720px;
  margin-bottom: 35px;
}
.subpage-header p {
  margin: 0 0 12px;
  color: var(--mint);
  font-size: 9px;
  font-weight: 700;
  letter-spacing: 2px;
}
.subpage-header h1 {
  margin: 0 0 12px;
  color: #fff7ed;
  font-size: clamp(32px, 4vw, 52px);
  font-weight: 800;
  letter-spacing: -2.6px;
}
.subpage-header h1 em {
  color: #ffd6a5;
  font-style: normal;
}
.subpage-header > span {
  color: rgba(255, 244, 232, 0.55);
  font-size: 11px;
}
.result-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 12px;
}
.result-game {
  padding: 20px 22px 15px;
  border: 1px solid var(--line);
  border-radius: 18px;
  background: rgba(31, 34, 53, 0.58);
  backdrop-filter: blur(18px);
  box-shadow: 0 18px 45px rgba(62, 28, 34, 0.14);
}
.result-game:last-child {
  grid-column: 1 / -1;
  max-width: calc(50% - 6px);
}
.result-meta {
  display: flex;
  justify-content: space-between;
  color: rgba(255, 243, 231, 0.45);
  font-size: 8px;
}
.result-meta b {
  color: #ffd6a5;
  font-weight: 600;
}
.result-score {
  display: grid;
  grid-template-columns: 1fr auto 1fr;
  align-items: center;
  gap: 18px;
  margin: 18px 0;
}
.result-score p {
  display: flex;
  flex-direction: column;
  margin: 0;
}
.result-score p.home {
  text-align: right;
}
.result-score small {
  color: rgba(255, 255, 255, 0.35);
  font-size: 7px;
}
.result-score strong {
  margin-top: 4px;
  color: #fff;
  font-size: 15px;
}
.result-score > div {
  display: flex;
  align-items: center;
  gap: 10px;
}
.result-score > div b {
  font-size: 29px;
}
.result-score > div i {
  color: var(--orange);
  font-style: normal;
}
.inning-line {
  display: grid;
  grid-template-columns: repeat(9, 1fr);
  gap: 3px;
}
.inning-line i {
  height: 2px;
  border-radius: 9px;
  background: rgba(255, 255, 255, 0.08);
}
.inning-line i:nth-child(-n + 5) {
  background: rgba(255, 189, 135, 0.42);
}
.team-selector {
  display: flex;
  flex-wrap: wrap;
  gap: 7px;
  margin-bottom: 25px;
}
.team-selector button {
  display: flex;
  align-items: center;
  gap: 7px;
  padding: 8px 12px 8px 8px;
  border: 1px solid var(--line);
  border-radius: 999px;
  color: rgba(255, 255, 255, 0.6);
  background: rgba(35, 34, 52, 0.48);
  font-size: 9px;
  cursor: pointer;
}
.team-selector button.active {
  border-color: color-mix(in srgb, var(--team-color) 75%, white);
  color: #fff;
  background: var(--team-color);
  box-shadow: 0 8px 24px color-mix(in srgb, var(--team-color) 32%, transparent);
}
.team-ball {
  display: grid;
  place-items: center;
  width: 21px;
  height: 21px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.12);
  font-size: 11px;
}
.myteam-hero {
  display: grid;
  grid-template-columns: auto 1fr auto;
  align-items: center;
  gap: 24px;
  padding: 30px;
  border: 1px solid color-mix(in srgb, var(--team-color) 65%, white);
  border-radius: 24px;
  background: linear-gradient(
    125deg,
    color-mix(in srgb, var(--team-color) 68%, #171b2c),
    color-mix(in srgb, var(--team-color) 36%, #3b2d3c)
  );
  backdrop-filter: blur(22px);
}
.team-monogram {
  display: grid;
  place-items: center;
  width: 84px;
  height: 84px;
  border: 1px solid rgba(255, 235, 210, 0.4);
  border-radius: 50%;
  color: #fff;
  background: var(--team-color);
  font-size: 22px;
  font-weight: 800;
  box-shadow: 0 0 38px color-mix(in srgb, var(--team-color) 48%, transparent);
}
.myteam-copy small {
  color: #ffd6a5;
  font-size: 8px;
  letter-spacing: 1px;
}
.myteam-copy h2 {
  margin: 5px 0 7px;
  color: #fff;
  font-size: 29px;
}
.myteam-copy p {
  margin: 0;
  color: rgba(255, 255, 255, 0.58);
  font-size: 10px;
}
.myteam-weather {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
}
.myteam-weather > span {
  color: #ffd080;
  font-size: 25px;
}
.myteam-weather strong {
  font-size: 31px;
}
.myteam-weather small {
  color: rgba(255, 255, 255, 0.45);
  font-size: 8px;
}
.team-empty {
  padding: 70px;
  border: 1px dashed var(--line);
  border-radius: 22px;
  color: rgba(255, 255, 255, 0.48);
  background: rgba(35, 34, 52, 0.3);
  font-size: 11px;
  text-align: center;
}

.status-dock {
  position: relative;
  z-index: 4;
  display: flex;
  align-items: center;
  gap: 13px;
  max-width: 1500px;
  margin: -8px auto 0;
  padding: 10px 14px;
  border: 1px solid var(--line);
  border-radius: 14px;
  color: rgba(255, 255, 255, 0.4);
  background: rgba(5, 19, 30, 0.64);
  backdrop-filter: blur(18px);
  font-size: 8px;
}
.status-ball {
  color: var(--mint);
  text-shadow: 0 0 9px var(--mint);
}
.status-dock p {
  flex: 1;
  margin: 0;
}
.status-dock > div {
  padding-left: 13px;
  border-left: 1px solid var(--line);
  letter-spacing: 0.8px;
}
.status-dock b {
  margin-right: 4px;
  color: #fff;
  font-size: 10px;
}

@media (max-width: 1050px) {
  .experience-layout {
    grid-template-columns: 250px 1fr;
  }
  .detail-panel {
    grid-column: 1/-1;
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 14px;
  }
  .detail-topline,
  .stadium-title,
  .matchup,
  .off-day {
    grid-column: 1;
  }
  .weather-dial,
  .weather-stats,
  .detail-button {
    grid-column: 2;
  }
  .weather-dial {
    grid-row: 1/4;
  }
  .weather-stats {
    grid-row: 4;
  }
  .detail-button {
    grid-row: 5;
  }
  .map-section {
    min-height: 640px;
  }
  .map-stage {
    height: 640px;
  }
}
@media (max-width: 760px) {
  .ballpark-app {
    padding: 22px 15px 80px;
    overflow: visible;
  }
  .live-badge {
    display: none;
  }
  .page-tabs {
    width: 100%;
    margin: 18px 0 0;
  }
  .page-tabs button {
    flex: 1;
    justify-content: center;
    padding: 8px 5px;
    font-size: 8px;
  }
  .experience-layout {
    display: flex;
    flex-direction: column;
    min-height: 0;
  }
  .intro-panel {
    width: 100%;
    padding-bottom: 0;
  }
  .intro-panel h1 {
    font-size: 43px;
  }
  .intro-copy {
    margin-bottom: 20px;
  }
  .search-field,
  .search-results {
    max-width: none;
  }
  .search-results {
    grid-template-columns: 1fr 1fr;
    max-height: none;
  }
  .map-section {
    width: 100%;
    min-height: 590px;
    order: 2;
  }
  .map-stage {
    height: 590px;
  }
  .map-orbit {
    display: none;
  }
  .stadium-marker {
    transform: translate(-50%, -50%) scale(0.9);
  }
  .map-tooltip {
    position: fixed;
    right: 15px !important;
    bottom: 80px;
    left: 15px !important;
    top: auto;
    width: auto;
  }
  .detail-panel {
    display: block;
    width: 100%;
    order: 3;
  }
  .detail-panel > * {
    grid-column: auto;
    grid-row: auto;
  }
  .subpage {
    min-height: calc(100vh - 120px);
    padding: 55px 0 60px;
  }
  .subpage-header h1 {
    font-size: 34px;
  }
  .result-grid {
    grid-template-columns: 1fr;
  }
  .result-game:last-child {
    grid-column: auto;
    max-width: none;
  }
  .myteam-hero {
    grid-template-columns: auto 1fr;
    padding: 22px;
  }
  .team-monogram {
    width: 60px;
    height: 60px;
    font-size: 17px;
  }
  .myteam-weather {
    grid-column: 1 / -1;
    flex-direction: row;
    align-items: center;
    justify-content: flex-end;
    gap: 9px;
  }
  .status-dock {
    position: fixed;
    right: 10px;
    bottom: 10px;
    left: 10px;
    z-index: 20;
    margin: 0;
  }
  .status-dock > div {
    display: none;
  }
  .baseball-moon {
    display: none;
  }
}
@media (prefers-reduced-motion: reduce) {
  .ballpark-app *,
  .ballpark-app *::before,
  .ballpark-app *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
</style>
