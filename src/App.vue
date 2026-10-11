<script setup>
  import { ref, onMounted, onUnmounted } from 'vue'

  const activeSection = ref(null)
  const sectionIds = ['about', 'skills', 'project', 'contact']
  let scrollFrame = null

  const updateActiveSection = () => {
    const marker = window.innerHeight * 0.35
    activeSection.value = sectionIds.find((id) => {
      const section = document.getElementById(id)
      if (!section) return false
      const bounds = section.getBoundingClientRect()
      return bounds.top <= marker && bounds.bottom > marker
    }) ?? null
    scrollFrame = null
  }

  const handleScroll = () => {
    if (scrollFrame === null) scrollFrame = window.requestAnimationFrame(updateActiveSection)
  }

  const selectedProject = ref(null)
  const projects = [
    {
      id: 'chapchap', title: '챱챱', image: 'chapchap.png', date: '2026. 08 - 2026. 09',
      role: '2차 팀 프로젝트 · 팀장 / Delivery Service', summary: '정기구독 푸드 커머스의 배송 도메인 설계·구현',
      description: '4인 팀 프로젝트에서 배송 요구사항과 상태 흐름을 설계하고, 배송 실행부터 이벤트 처리까지 담당했습니다.',
      tags: ['Java 21', 'Spring Boot', 'JPA', 'MySQL', 'Kafka', 'Flyway', 'MinIO', 'SSE'],
      notionUrl: 'https://app.notion.com/p/CHAPCHAP-3e9756542e3c81cc9d41d5ba7bffe576?pvs=25',
      features: [
        { title: '배송 상태 전이 제어', detail: '시작·완료·실패·지연 흐름을 설계하고, 현재 상태를 조건으로 둔 원자적 UPDATE로 동시 요청 중 하나만 상태 전이에 성공하도록 처리했습니다.' },
        { title: '멱등성과 충돌 처리', detail: '배송 완료 요청이 재전송될 때 동일 payload는 기존 결과를 반환하고, 다른 payload는 409 Conflict로 구분했습니다.' },
        { title: 'Kafka·MinIO 연동', detail: '배송·환불 이벤트를 Kafka로 처리하고, 배송 완료 사진은 MinIO에 저장했습니다. DB 처리 실패 시 파일을 보상 삭제해 정합성을 유지했습니다.' },
        { title: '라이더 배정과 실시간 위치 전달', detail: '라이더 배정·재배정과 위치 수신을 구현하고, Rider Vue → HTTP → Delivery Service → SSE로 고객에게 위치를 전달했습니다.' },
      ],
    },
    {
      id: 'tourswitch', title: '투어 스위치', image: 'tourswitch.png', date: '2026. 08 - 2026. 09',
      role: '관광데이터 활용 공모전 · 기획 / Figma / Full-stack', summary: '혼잡도를 고려해 함께 계획하는 서울 여행 서비스',
      description: '여행 동행자를 초대하고 관광지 후보를 투표한 뒤, 확정된 여행 정보를 기록으로 확인할 수 있도록 사용자 흐름을 구성했습니다.',
      tags: ['Vue 3', 'Pinia', 'Axios', 'Kakao Map', 'Spring Boot', 'JPA', 'OAuth2', 'JWT', 'MySQL'],
      notionUrl: 'https://app.notion.com/p/TOURSWITCH-3e9756542e3c817b8a6eebe486a1daa0?pvs=25',
      features: [
        { title: '여행방 생성과 초대 흐름', detail: '지역·날짜·관심 키워드로 여행방을 만들고, 초대 링크를 공유해 동행자가 여행 계획에 참여하도록 구현했습니다.' },
        { title: '관광지 탐색과 투표 UI', detail: '카테고리와 혼잡도·지도를 바탕으로 관광지를 탐색하고, 후보 카드에서 선택·투표하는 화면을 담당했습니다.' },
        { title: 'Kakao 로그인과 회원 인증', detail: 'Kakao OAuth2 로그인 후 회원을 연동하고, JWT Access·Refresh Token 기반으로 인증 상태를 유지하도록 구현했습니다.' },
        { title: '여행 기록과 방 관리', detail: '투표방 삭제와 상태 관리, 확정된 여행의 코스·방문 정보를 확인하는 Travel History 조회 기능을 구현했습니다.' },
      ],
    },
    {
      id: 'sangdari', title: '상다리', image: 'sangdari.png', date: '2026. 06 - 2026. 07',
      role: '1차 팀 프로젝트 · 팀장 / 회원 기능 Full-stack', summary: '기업·행사 고객과 푸드트럭 사업자를 연결하는 매칭 서비스',
      description: '첫 Full-stack 팀 프로젝트에서 회원 기능의 화면, API, DB를 연결하고 팀 일정과 문서 관리도 함께 담당했습니다.',
      tags: ['Vue 3', 'Pinia', 'Java', 'Spring Boot', 'MyBatis', 'MySQL'],
      notionUrl: 'https://app.notion.com/p/SANGDARI-3e9756542e3c81af9905ca3df01296d9?pvs=25',
      features: [
        { title: '회원 기능 전체 흐름', detail: '회원가입·입력값 검증·이메일 중복 확인·로그인·비밀번호 변경·내 정보 조회 및 수정을 화면부터 API, DB까지 구현했습니다.' },
        { title: '데이터 흐름 기반 디버깅', detail: '화면에 데이터가 보이지 않는 문제를 DB → API → Network → Vue 상태 → v-model 순서로 확인해 바인딩 누락을 해결했습니다.' },
        { title: '인증 상태와 라우팅 분리', detail: '회원가입 후 메인으로 이동하던 문제를 분석해 회원가입과 로그인 상태 변경의 책임을 분리하고 Router Guard 흐름을 정리했습니다.' },
        { title: '팀장 역할과 협업 관리', detail: 'ERD 설계와 함께 Notion 일정·회의록을 관리했습니다. 이후 프로젝트에서는 진행 상황과 역할을 더 자주 공유하는 방식으로 개선했습니다.' },
      ],
    },
  ]

  const closeModal = () => { selectedProject.value = null }
  const handleEscape = (event) => { if (event.key === 'Escape') closeModal() }
  onMounted(() => {
    window.addEventListener('keydown', handleEscape)
    window.addEventListener('scroll', handleScroll, { passive: true })
    window.addEventListener('resize', handleScroll)
    updateActiveSection()
  })
  onUnmounted(() => {
    window.removeEventListener('keydown', handleEscape)
    window.removeEventListener('scroll', handleScroll)
    window.removeEventListener('resize', handleScroll)
    if (scrollFrame !== null) window.cancelAnimationFrame(scrollFrame)
  })

  const skills = [
    { name: 'HTML5', level: 80 },
    { name: 'CSS3', level: 80 },
    { name: 'JavaScript', level: 70 },
    { name: 'Vue3', level: 50 },
    { name: 'Java', level: 50 },
    { name: 'Spring Boot', level: 60 },
    { name: 'MySQL', level: 70 },
    { name: 'Docker', level: 20 },
    { name: 'K8S', level: 20 },
  ]

  const getLevelClass = (level) => {
    if (level >= 80) {
      return 'red'
    }

    if (level >= 50) {
      return 'blue'
    }

    return 'yellow'
  }

  const getLevelLabel = (level) => {
    if (level >= 80) {
      return '익숙하게 활용'
    }

    if (level >= 50) {
      return '프로젝트 경험'
    }

    return '기초 학습'
  }
</script>

<template>
  <main>
    <nav>
      <ul>
        <li><a href="#about" class="nav-about" :class="{ 'is-active': activeSection === 'about' }">< ABOUT /></a></li>
        <li><a href="#skills" class="nav-skills" :class="{ 'is-active': activeSection === 'skills' }">< SKILLS /></a></li>
        <li><a href="#project" class="nav-project" :class="{ 'is-active': activeSection === 'project' }">< PROJECT /></a></li>
        <li><a href="#contact" class="nav-contact" :class="{ 'is-active': activeSection === 'contact' }">< CONTACT /></a></li>
      </ul>
    </nav>

    <section id="hero">
      <div class="pr-container">
        <div class="symbol">
          <span class="brace">{</span>
          <span class="mark">
            <div class="q">?</div>
            <div class="e">!</div>
            <div class="p">.</div>
          </span>
          <span class="brace">}</span>
        </div>

        <div class="slogan">
          <h1 class="typing desk">
            <span class="brace">{ </span>
            <span class="slogan-error txt-hover"> 오류 </span>를 '성공'으로 바꾸는 집요함, 
            <span class="slogan-q txt-hover mark-hover">물음표</span>를 
            <span class="slogan-e txt-hover mark-hover">느낌표</span>로 바꾸는 
            <span class="slogan-p txt-hover mark-hover">'마침표'</span>같은 개발자 
            <span class="slogan-hippie txt-hover">조 은 혜</span>입니다.
            <span class="brace"> }</span>
          </h1>

          <!-- 태블릿 -->
           <div class="t-typing">
            <p>
              <span class="brace">{ </span>
              <span class="slogan-error txt-hover"> 오류 </span>를 '성공'으로 바꾸는 집요함, 
            </p>
            <p>
              <span class="slogan-q txt-hover mark-hover">물음표</span>를 
              <span class="slogan-e txt-hover mark-hover">느낌표</span>로 바꾸는 
              <span class="slogan-p txt-hover mark-hover">'마침표'</span>같은 개발자 
              <span class="slogan-hippie txt-hover">조 은 혜</span> 입니다.
              <span class="brace"> }</span>
            </p>
          </div>

          <!-- 모바일 -->
          <div class="m-typing">
            <p>
              <span class="brace">{ </span>
              <span class="slogan-error txt-hover m-color-error"> 오류 </span>를 '성공'으로 바꾸는 집요함, 
            </p>
            <p>
              <span class="slogan-q txt-hover mark-hover m-color-q">물음표</span>를 
              <span class="slogan-e txt-hover mark-hover m-color-e">느낌표</span>로 바꾸는 
              <span class="slogan-p txt-hover mark-hover m-color-p">'마침표'</span>같은
            </p>
            <p>
              개발자 
              <span class="slogan-hippie txt-hover m-color-h">조 은 혜</span> 입니다.
              <span class="brace"> }</span>
            </p>
          </div>
        </div>
      </div>
    </section>
    
    <section id="about">
      <h2 class="title">ABOUT</h2>
      <div class="about-container">
        <div class="about-wrap">
          <div class="about-photo"></div>
          <ul class="about-profile">
            <li>조 은 혜</li>
            <li>Junior Full-Stack Developer</li>
            <li>1996. 03. 19</li>
          </ul>
        </div>
        <div class="about-intro">
          <div class="about-txt">
            <p>
              사용자의 행동에 따라 화면이 변화하는 인터랙티브 요소에 관심이 많았습니다.<br>
              코딩을 배우며 생각한 것을 직접 구현할 수 있다는 사실에 매력을 느꼈고,<br>
              서버와 데이터가 연결되어 하나의 기능이 완성되는 과정에도 흥미를 갖게 되었습니다.
            </p>
          </div>
          <span class="brace">{ <span class="p">.</span> }</span>
          <div class="intro-txt">
            <p>4인 팀 프로젝트의 팀장을 맡아 역할 조율과 일정·문서 관리를 경험하며,</p>
            <p>주문·배달 관련 서비스를 기획하고 개발했습니다.</p>
            <p>또한 공모전에서는 디자인과 주요 기능 개발로 서비스 구현 경험을 넓혔습니다.</p>
            <p>다양한 의견을 가진 팀원들과 협업한 경험을 바탕으로</p>
            <p>문제를 끝까지 해결하고 팀과 함께 서비스를 완성하는 개발자가 되겠습니다.</p>
          </div>
        </div>
      </div>
    </section>

    <section id="skills" :class="{ 'skills-in-view': activeSection === 'skills' }">
      <h2 class="title">SKILLS</h2>
      <div class="skills-container">
        <div
          v-for="skill in skills"
          :key="skill.name"
          class="skill"
        >
          <div class="skill-name">
            {{ skill.name }}
          </div>

          <div class="skill-bar">
            <div
              class="skill-progress"
              :class="getLevelClass(skill.level)"
              :style="{ '--skill-width': `${skill.level}%` }"
            >
              <span class="skill-txt">
                {{ getLevelLabel(skill.level) }}
              </span>
            </div>
          </div>
        </div>
      </div>
    </section>

    <section id="project">
      <h2 class="title">PROJECT</h2>
      <div class="porj-card">
        <article v-for="project in projects" :key="project.id" class="content-box">
          <div class="img-wrapper" :style="{ backgroundImage: `url(${project.image})` }" aria-hidden="true"></div>
          <div class="project-copy">
            <p class="proj-role">{{ project.role }}</p>
            <h3 class="proj-title">{{ project.title }}</h3>
            <p class="proj-summary">{{ project.summary }}</p>
            <div class="readme-button" aria-hidden="true">
              <span>README.md 보기</span><span aria-hidden="true">↗</span>
            </div>
          </div>
          <button class="card-hit-area" type="button" :aria-label="`${project.title} README 보기`" @click="selectedProject = project"></button>
        </article>
      </div>
    </section>

    <div v-if="selectedProject" class="project-modal-backdrop" @click.self="closeModal">
      <section class="project-modal" role="dialog" aria-modal="true" :aria-label="`${selectedProject.title} 프로젝트 상세`">
        <div class="modal-heading-row">
          <span class="modal-label">PROJECT ARCHIVE</span>
          <button class="modal-close" type="button" aria-label="프로젝트 상세 닫기" @click="closeModal">닫기 ×</button>
        </div>
        <h2>{{ selectedProject.title }}</h2>
        <p class="modal-subtitle">{{ selectedProject.summary }}</p>
        <p class="modal-intro">{{ selectedProject.description }}</p>
        <dl class="modal-facts">
          <div><dt>프로젝트 기간</dt><dd>{{ selectedProject.date }}</dd></div>
          <div><dt>참여 역할</dt><dd>{{ selectedProject.role }}</dd></div>
        </dl>
        <div class="modal-tags" aria-label="사용 기술">
          <span v-for="tag in selectedProject.tags" :key="tag">{{ tag }}</span>
        </div>
        <a class="modal-resource" :href="selectedProject.notionUrl" target="_blank" rel="noopener noreferrer">프로젝트 상세 기록 보기 <span aria-hidden="true">↗</span></a>
        <div class="modal-features">
          <h3>주요 기능과 구현 내용</h3>
          <p class="modal-features-intro">프로젝트에서 다룬 핵심 기능과 기술입니다.</p>
          <ol>
            <li v-for="(feature, index) in selectedProject.features" :key="feature.title">
              <span class="feature-number">{{ String(index + 1).padStart(2, '0') }}</span>
              <div><h4>{{ feature.title }}</h4><p>{{ feature.detail }}</p></div>
            </li>
          </ol>
          <figure class="modal-preview">
            <img :src="selectedProject.image" :alt="`${selectedProject.title} 프로젝트 화면`" />
            <figcaption>{{ selectedProject.title }} 프로젝트 화면</figcaption>
          </figure>
        </div>
      </section>
    </div>

    <section id="contact">
      <h2 class="title">CONTACT</h2>
      <div class="contact-content">
        <div class="contact-links">
          <a class="contact-card" href="https://github.com/j-hippie-dev" target="_blank" rel="noopener noreferrer">
            <span class="contact-card-label">GITHUB</span>
          </a>
          <a class="contact-card" href="https://app.notion.com/p/PORTFOLIO-3e3756542e3c802d8e4bc275260bfbee?source=copy_link" target="_blank" rel="noopener noreferrer">
            <span class="contact-card-label">PORTFOLIO NOTION</span>
          </a>
        </div>
      </div>
    </section>
  </main>
  <footer>
    <p class="copy">Copyright 2026. HIPPIE Co. All rights reserved.</p>
  </footer>
</template>
