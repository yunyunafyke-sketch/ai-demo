<script setup>
import { computed, onBeforeUnmount, onMounted, ref } from "vue";

function formatDate(value) {
  const date = new Date(value);
  if (Number.isNaN(date.getTime())) return value;

  const year = date.getFullYear();
  const month = String(date.getMonth() + 1).padStart(2, "0");
  const day = String(date.getDate()).padStart(2, "0");

  return `${year}.${month}.${day}`;
}

function countDaysSince(value) {
  const start = new Date(value);
  if (Number.isNaN(start.getTime())) return null;

  const now = new Date();
  const startMidnight = new Date(start.getFullYear(), start.getMonth(), start.getDate());
  const nowMidnight = new Date(now.getFullYear(), now.getMonth(), now.getDate());
  const diff = nowMidnight - startMidnight;

  return Math.max(0, Math.floor(diff / 86400000));
}

function animateNumber(target, onUpdate) {
  const duration = 1400;
  const startTime = performance.now();

  function step(now) {
    const progress = Math.min((now - startTime) / duration, 1);
    const eased = 1 - Math.pow(1 - progress, 3);
    onUpdate(String(Math.round(target * eased)));

    if (progress < 1) {
      requestAnimationFrame(step);
    }
  }

  requestAnimationFrame(step);
}

function setupFadeIn() {
  const observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          entry.target.classList.add("visible");
        }
      });
    },
    { threshold: 0.14 }
  );

  document.querySelectorAll(".fade-up").forEach((element) => observer.observe(element));
  return observer;
}

const profile = ref({
  brandTitle: "我们的二人轨道",
  eyebrow: "Private Edition / 只此一份",
  heroTitleTop: "刚好是你",
  heroTitleAccent: "也是唯一",
  heroText:
    "这不是一张通用的恋爱页面，而是一份可以慢慢替换成你们故事的私人星图。每一段靠近、每一次心动、每一个未来计划，都被安放在只属于你们的轨道里。",
  names: ["你", "TA"],
  nicknameSignal: "Moonline",
  locationCode: "SH-LOVE",
  centerLabel: "把喜欢写得安静一点\n也写得长久一点",
  startDate: "2024-02-14",
  footerQuote: "把喜欢写得克制一点，反而更显得长久。",
  keywords: ["偏爱长期有效", "一起逃离无聊", "情绪有人接住", "吵完也还是想拥抱", "再忙也要分享今天"],
  timeline: [
    {
      date: "第一次对上频率",
      title: "原来聊天也会有引力",
      text: "从一开始的试探，到后来每一句都想秒回。你们不是突然熟悉，而是在无数个细节里变成了彼此默认的特别。"
    },
    {
      date: "真正开始靠近",
      title: "从想见面，到见面后还想再见",
      text: "那些并不轰轰烈烈的时刻，反而最容易留下痕迹。一起走路、一起吃饭、一起把沉默也过得很舒服。"
    },
    {
      date: "关系被认真命名",
      title: "喜欢终于有了明确去向",
      text: "不是因为合适才在一起，而是因为你们愿意把彼此放进未来，所以关系开始拥有了重量。"
    }
  ],
  letter: [
    "致我最偏爱的你：",
    "谢谢你出现在我本来很普通的生活里，把平凡的日子一点点点亮。你不是把我从世界里带走的人，你是让我更愿意留在这个世界里的人。",
    "我喜欢我们之间那些不需要解释的默契，喜欢你下意识的关心，喜欢我一开口你就知道我想说什么。更喜欢的是，不管今天顺不顺利，最后想到你，心就会慢慢安静下来。",
    "如果以后还有很多复杂、很多辛苦、很多需要并肩面对的时刻，我也还是想站在你旁边。不是因为浪漫话好听，而是因为你值得我认真、长久、反复地选择。"
  ],
  promises: [
    {
      title: "情绪有回音",
      text: "开心要第一时间告诉你，难过也不会一个人消化到天亮。"
    },
    {
      title: "争吵不取消爱",
      text: "可以有分歧，但不冷暴力，不故意输掉彼此。"
    },
    {
      title: "仪式感不等节日",
      text: "不只是在特别的日子说爱你，也在普通星期二认真对你好。"
    }
  ],
  futurePlans: [
    {
      title: "一起去看新的城市夜景",
      text: "最好风有点大，路有点绕，但身边的人刚好是你。"
    },
    {
      title: "做一本只有我们看得懂的相册",
      text: "照片可以普通，但每一张背后的故事都要独一无二。"
    },
    {
      title: "把“以后”说得更具体一点",
      text: "不只是想象未来，而是一起把未来安排进真实生活。"
    }
  ]
});

const animatedDays = ref("0");
const visibleKeywords = computed(() => profile.value.keywords.slice(0, 4));
const displayNames = computed(() => `${profile.value.names[0]} × ${profile.value.names[1]}`);
const centerLabelHtml = computed(() => profile.value.centerLabel.replace("\n", "<br />"));
const formattedStartDate = computed(() => formatDate(profile.value.startDate));
const letterParagraphs = computed(() => profile.value.letter.slice(1));
const footerNames = computed(() => `${profile.value.names[0]} & ${profile.value.names[1]}`);
const footerMessage = computed(() => " 的页面，把喜欢写得更安静一点。");
const footerYear = computed(() => `Made for ${new Date().getFullYear()}`);

let observer;

onMounted(() => {
  const totalDays = countDaysSince(profile.value.startDate);
  animatedDays.value = totalDays === null ? "---" : "0";

  if (totalDays !== null) {
    animateNumber(totalDays, (value) => {
      animatedDays.value = value;
    });
  }

  observer = setupFadeIn();
});

onBeforeUnmount(() => {
  observer?.disconnect();
});
</script>

<template>
  <div class="page">
    <nav class="nav fade-up">
      <div class="brand">
        <div class="brand-mark" aria-hidden="true"></div>
        <span>{{ profile.brandTitle }}</span>
      </div>
      <div class="nav-links">
        <a href="#timeline">故事切片</a>
        <a href="#letter">专属情书</a>
        <a href="#future">未来清单</a>
      </div>
    </nav>

    <section class="hero">
      <article class="panel hero-copy fade-up">
        <div>
          <div class="eyebrow">
            <span class="eyebrow-dot" aria-hidden="true"></span>
            <span>{{ profile.eyebrow }}</span>
          </div>
          <h1 class="hero-title">
            {{ profile.heroTitleTop }}
            <em>{{ profile.heroTitleAccent }}</em>
          </h1>
          <p class="hero-text">{{ profile.heroText }}</p>
          <div class="chips">
            <span v-for="keyword in visibleKeywords" :key="keyword" class="chip">{{ keyword }}</span>
          </div>
          <div class="hero-actions">
            <a class="btn btn-primary" href="#letter">读这封情书</a>
            <a class="btn btn-secondary" href="#future">查看未来清单</a>
          </div>
        </div>
      </article>

      <aside class="panel hero-visual fade-up">
        <div class="hero-summary">
          <div>
            <div class="summary-kicker">Private Edition</div>
            <h2 class="summary-title">{{ displayNames }}</h2>
          </div>
          <div class="summary-intro" v-html="centerLabelHtml"></div>
          <div class="summary-list">
            <div class="summary-row">
              <span class="summary-label">已经一起走过</span>
              <span class="summary-value large">{{ animatedDays }}</span>
            </div>
            <div class="summary-row">
              <span class="summary-label">纪念日</span>
              <span class="summary-value">{{ formattedStartDate }}</span>
            </div>
            <div class="summary-row">
              <span class="summary-label">专属暗号</span>
              <span class="summary-value">{{ profile.nicknameSignal }}</span>
            </div>
            <div class="summary-row">
              <span class="summary-label">小宇宙坐标</span>
              <span class="summary-value">{{ profile.locationCode }}</span>
            </div>
          </div>
          <p class="summary-note">{{ profile.footerQuote }}</p>
        </div>
      </aside>
    </section>

    <section id="timeline" class="section fade-up">
      <div class="section-header">
        <div>
          <h2 class="section-title">故事切片</h2>
          <p class="section-subtitle">
            有些关系不是突然开始的，而是在一次次对视、一次次靠近、一次次“下次还想见面”里慢慢发光。
          </p>
        </div>
        <div class="mini-tag">Orbit Log / Private Archive</div>
      </div>
      <div class="timeline">
        <article v-for="item in profile.timeline" :key="item.title" class="timeline-card">
          <div>
            <div class="timeline-date">{{ item.date }}</div>
            <div class="timeline-title">{{ item.title }}</div>
          </div>
          <div class="timeline-text">{{ item.text }}</div>
        </article>
      </div>
    </section>

    <section class="split-grid">
      <article id="letter" class="panel letter-panel fade-up">
        <div class="letter-shell">
          <div class="letter-top">
            <div>
              <div class="section-title letter-section-title">专属情书</div>
              <div class="letter-status">写给彼此，也只留给彼此。</div>
            </div>
          </div>
          <div class="letter-content">
            <div class="letter-greeting">{{ profile.letter[0] }}</div>
            <p v-for="paragraph in letterParagraphs" :key="paragraph">{{ paragraph }}</p>
          </div>
        </div>
      </article>

      <article id="future" class="panel future-panel fade-up">
        <div class="section-header">
          <div>
            <h2 class="section-title future-section-title">未来清单</h2>
            <p class="section-subtitle future-section-subtitle">
              喜欢一件事很容易，难得的是愿意一起把很多平常又具体的以后认真排进日程里。
            </p>
          </div>
        </div>
        <div class="list-label">想认真做到的事</div>
        <div class="promise-list">
          <div v-for="(item, index) in profile.promises" :key="item.title" class="promise-item">
            <div class="promise-badge">{{ index + 1 }}</div>
            <div>
              <div class="promise-title">{{ item.title }}</div>
              <div class="promise-text">{{ item.text }}</div>
            </div>
          </div>
        </div>
        <div class="list-label">想一起去的以后</div>
        <div class="future-list">
          <div v-for="item in profile.futurePlans" :key="item.title" class="future-item">
            <div class="future-badge">+</div>
            <div>
              <div class="future-title">{{ item.title }}</div>
              <div class="future-text">{{ item.text }}</div>
            </div>
          </div>
        </div>
      </article>
    </section>

    <footer class="footer fade-up">
      <div>
        <strong>{{ footerNames }}</strong>
        <span>{{ footerMessage }}</span>
      </div>
      <div>{{ footerYear }}</div>
    </footer>
  </div>
</template>

<style>
body {
  min-height: 100vh;
  overflow-x: hidden;
  color: var(--text);
  font-family: "Manrope", sans-serif;
  background:
    radial-gradient(circle at 10% 10%, rgba(255, 255, 255, 0.58), transparent 24%),
    radial-gradient(circle at 85% 12%, rgba(246, 176, 198, 0.5), transparent 22%),
    radial-gradient(circle at 54% 90%, rgba(240, 185, 205, 0.34), transparent 28%),
    linear-gradient(180deg, #fff3f7 0%, #f9e2ea 48%, #f2d1dd 100%);
}

body::before {
  content: "";
  position: fixed;
  inset: 0;
  z-index: -2;
  opacity: 0.56;
  pointer-events: none;
  background:
    linear-gradient(180deg, rgba(255, 255, 255, 0.38), transparent 28%),
    linear-gradient(90deg, rgba(129, 72, 96, 0.03) 1px, transparent 1px);
  background-size: auto, 160px 160px;
}

:root {
  --bg: #f9edf1;
  --bg-soft: #f6dce5;
  --panel: rgba(255, 247, 250, 0.76);
  --panel-strong: rgba(255, 245, 248, 0.92);
  --line: rgba(129, 72, 96, 0.12);
  --text: #432534;
  --muted: rgba(67, 37, 52, 0.68);
  --peach: #e191ac;
  --rose: #c76989;
  --gold: #b98761;
  --cyan: #8ab8c8;
  --shadow: 0 28px 70px rgba(121, 84, 100, 0.09);
  --radius-xl: 32px;
  --radius-lg: 24px;
  --radius-md: 18px;
  --max-width: 1180px;
}

a {
  color: inherit;
  text-decoration: none;
}

button {
  font: inherit;
}

.page {
  width: min(calc(100% - 56px), 1220px);
  margin: 0 auto;
  padding: 22px 0 72px;
}

.nav {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 16px;
  margin-bottom: 42px;
  padding: 10px 0 20px;
  border-bottom: 1px solid var(--line);
}

.brand {
  display: flex;
  align-items: center;
  gap: 10px;
  font-weight: 700;
  letter-spacing: 0.06em;
}

.brand-mark {
  width: 14px;
  height: 14px;
  border-radius: 50%;
  background: linear-gradient(135deg, #f6c4d5, #d97f9a);
  box-shadow: 0 0 0 6px rgba(217, 127, 154, 0.14);
}

.nav-links {
  display: flex;
  align-items: center;
  gap: 28px;
  color: var(--muted);
  font-size: 0.88rem;
  letter-spacing: 0.08em;
  text-transform: uppercase;
}

.nav-links a:hover {
  color: var(--text);
}

.hero {
  display: grid;
  grid-template-columns: 1.15fr 0.85fr;
  gap: 56px;
  align-items: start;
}

.panel {
  position: relative;
  overflow: hidden;
  border: 1px solid var(--line);
  border-radius: 28px;
  background: linear-gradient(180deg, rgba(255, 255, 255, 0.58), rgba(255, 242, 247, 0.74)), var(--panel);
  box-shadow: var(--shadow);
  backdrop-filter: blur(18px);
}

.panel::after {
  display: none;
}

.hero-copy {
  display: flex;
  min-height: auto;
  flex-direction: column;
  justify-content: flex-start;
  padding: 26px 0 10px;
  border: 0;
  background: transparent;
  box-shadow: none;
  backdrop-filter: none;
}

.hero-copy::after {
  display: none;
}

.eyebrow {
  display: inline-flex;
  width: fit-content;
  align-items: center;
  gap: 10px;
  padding: 0;
  color: var(--rose);
  font-size: 0.84rem;
  letter-spacing: 0.08em;
  text-transform: uppercase;
}

.eyebrow-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: linear-gradient(135deg, #f0b2c6, var(--rose));
  box-shadow: none;
}

.hero-title {
  max-width: 8ch;
  margin: 22px 0;
  font-family: "Noto Serif SC", serif;
  font-size: clamp(4rem, 8vw, 7rem);
  line-height: 0.92;
  letter-spacing: -0.04em;
}

.hero-title em {
  display: block;
  color: var(--rose);
  font-family: "Instrument Serif", serif;
  font-style: italic;
  font-weight: 400;
  letter-spacing: 0;
}

.hero-text {
  max-width: 32rem;
  color: var(--muted);
  font-size: 1.08rem;
  line-height: 1.95;
}

.chips {
  display: flex;
  flex-wrap: wrap;
  gap: 12px 22px;
  margin-top: 34px;
}

.chip {
  position: relative;
  padding: 0;
  color: var(--muted);
  font-size: 0.92rem;
}

.chip::before {
  margin-right: 10px;
  content: "/";
  color: rgba(67, 37, 52, 0.32);
}

.hero-actions {
  display: flex;
  flex-wrap: wrap;
  gap: 14px;
  margin-top: 42px;
}

.btn {
  display: inline-flex;
  min-height: 48px;
  align-items: center;
  justify-content: center;
  gap: 10px;
  padding: 0 20px;
  border: 1px solid var(--line);
  border-radius: 999px;
  background: rgba(255, 245, 248, 0.68);
  transition:
    transform 0.25s ease,
    box-shadow 0.25s ease,
    border-color 0.25s ease,
    background 0.25s ease;
}

.btn:hover {
  transform: translateY(-1px);
  box-shadow: 0 12px 28px rgba(75, 39, 56, 0.08);
}

.btn-primary {
  border-color: #c76989;
  background: #c76989;
  color: #fff8fb;
  box-shadow: none;
}

.btn-secondary {
  background: rgba(255, 241, 246, 0.46);
}

.hero-visual {
  min-height: auto;
  padding: 34px;
}

.hero-summary {
  display: grid;
  gap: 26px;
}

.summary-kicker {
  color: var(--muted);
  font-size: 0.84rem;
  letter-spacing: 0.12em;
  text-transform: uppercase;
}

.summary-title {
  margin: 0;
  font-family: "Noto Serif SC", serif;
  font-size: clamp(2.2rem, 4vw, 3.3rem);
  line-height: 1.12;
}

.summary-intro {
  color: var(--muted);
  line-height: 1.85;
}

.summary-list {
  display: grid;
}

.summary-row {
  display: flex;
  align-items: end;
  justify-content: space-between;
  gap: 18px;
  padding: 18px 0;
  border-top: 1px solid var(--line);
}

.summary-label {
  color: var(--muted);
  font-size: 0.94rem;
}

.summary-value {
  font-weight: 700;
  text-align: right;
}

.summary-value.large {
  font-size: clamp(2rem, 4vw, 3rem);
  line-height: 1;
}

.summary-note {
  margin: 0;
  padding-top: 4px;
  color: var(--muted);
  line-height: 1.8;
}

.section {
  margin-top: 76px;
  padding: 0;
}

.section-header {
  display: flex;
  align-items: end;
  justify-content: space-between;
  gap: 20px;
  margin-bottom: 30px;
}

.section-title {
  margin: 0;
  font-family: "Noto Serif SC", serif;
  font-size: clamp(2.1rem, 4vw, 3.1rem);
  line-height: 1.1;
}

.section-subtitle {
  max-width: 42rem;
  margin: 10px 0 0;
  color: var(--muted);
  line-height: 1.8;
}

.mini-tag {
  white-space: nowrap;
  color: var(--muted);
  font-size: 0.84rem;
  letter-spacing: 0.1em;
  text-transform: uppercase;
}

.timeline {
  display: grid;
  gap: 0;
  border-top: 1px solid var(--line);
}

.timeline-card {
  display: grid;
  min-height: 0;
  grid-template-columns: 180px 1fr;
  gap: 24px;
  padding: 28px 0;
  border-bottom: 1px solid var(--line);
}

.timeline-card:nth-child(2),
.timeline-card:nth-child(3) {
  grid-column: auto;
}

.timeline-card:hover {
  transform: none;
}

.timeline-date {
  color: var(--rose);
  font-size: 0.85rem;
  letter-spacing: 0.08em;
  text-transform: uppercase;
}

.timeline-title {
  margin: 10px 0 8px;
  font-size: 1.45rem;
  font-weight: 800;
}

.timeline-text {
  max-width: 44rem;
  color: var(--muted);
  line-height: 1.8;
}

.split-grid {
  display: grid;
  grid-template-columns: 1.08fr 0.92fr;
  gap: 28px;
  margin-top: 64px;
}

.letter-panel,
.future-panel {
  min-height: auto;
  padding: 34px;
}

.letter-shell {
  position: relative;
  height: 100%;
  overflow: hidden;
  border: 1px solid rgba(184, 93, 122, 0.14);
  border-radius: 24px;
  background: linear-gradient(180deg, rgba(255, 255, 255, 0.72), rgba(255, 238, 244, 0.82));
  padding: 30px;
}

.letter-shell::before {
  display: none;
}

.letter-top {
  margin-bottom: 22px;
}

.letter-section-title,
.future-section-title {
  font-size: 2rem;
}

.letter-status {
  margin-top: 10px;
  color: var(--muted);
  font-size: 0.9rem;
}

.letter-content {
  position: relative;
  z-index: 1;
  display: grid;
  gap: 18px;
  color: var(--muted);
  line-height: 1.95;
}

.letter-greeting {
  color: var(--text);
  font-family: "Instrument Serif", serif;
  font-size: 2rem;
}

.future-panel {
  display: flex;
  flex-direction: column;
  gap: 18px;
}

.future-section-subtitle {
  margin-top: 12px;
}

.list-label {
  color: var(--muted);
  font-size: 0.84rem;
  letter-spacing: 0.1em;
  text-transform: uppercase;
}

.promise-list,
.future-list {
  display: grid;
  gap: 0;
}

.promise-item,
.future-item {
  display: flex;
  align-items: flex-start;
  gap: 16px;
  padding: 18px 0;
  border-bottom: 1px solid var(--line);
}

.promise-badge,
.future-badge {
  display: grid;
  width: 30px;
  height: 30px;
  flex: 0 0 auto;
  place-items: center;
  border: 1px solid rgba(184, 93, 122, 0.16);
  border-radius: 50%;
  background: rgba(239, 184, 199, 0.24);
  color: var(--rose);
  font-size: 0.9rem;
  font-weight: 700;
}

.future-badge {
  background: rgba(236, 169, 191, 0.2);
}

.promise-title,
.future-title {
  margin-bottom: 6px;
  font-weight: 800;
}

.promise-text,
.future-text {
  color: var(--muted);
  line-height: 1.7;
}

.footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 18px;
  margin-top: 72px;
  padding: 20px 0 0;
  border-top: 1px solid var(--line);
  color: var(--muted);
}

.footer strong {
  color: var(--text);
}

.fade-up {
  opacity: 0;
  transform: translateY(26px);
  transition:
    opacity 0.6s ease,
    transform 0.6s ease;
}

.fade-up.visible {
  opacity: 1;
  transform: translateY(0);
}

@media (max-width: 1024px) {
  .hero,
  .split-grid {
    grid-template-columns: 1fr;
  }

  .hero {
    gap: 34px;
  }

  .timeline-card {
    grid-template-columns: 140px 1fr;
  }

  .footer {
    flex-direction: column;
    align-items: flex-start;
  }
}

@media (max-width: 720px) {
  .page {
    width: min(calc(100% - 24px), 1220px);
    padding-top: 14px;
  }

  .nav {
    flex-direction: column;
    align-items: flex-start;
    gap: 14px;
    padding-bottom: 18px;
  }

  .nav-links {
    flex-wrap: wrap;
    gap: 14px;
  }

  .hero-title {
    font-size: clamp(3rem, 16vw, 4.8rem);
  }

  .hero-text {
    font-size: 0.98rem;
  }

  .chips {
    gap: 10px 18px;
  }

  .hero-visual,
  .letter-panel,
  .future-panel {
    padding: 22px;
  }

  .timeline-card {
    grid-template-columns: 1fr;
    gap: 12px;
  }

  .section-header {
    flex-direction: column;
    align-items: flex-start;
  }

  .footer {
    margin-top: 52px;
  }
}
</style>
