---
layout: page
title: fun stuff
permalink: /fun-stuff/
description: Fun stuff outside academia
nav: true            # ← 让它出现在导航栏
nav_order: 7        # ← 排序：数字越小越靠左（按需改，比如 60/70/90）
---
<!-- ============ Carousel ============ -->
<div class="vm-carousel" id="funCarousel" tabindex="0" aria-label="Photo carousel">
  <button class="vm-prev" aria-label="Previous slide">‹</button>

  <div class="vm-track" role="group" aria-roledescription="carousel">
    {% for p in site.data.fun_photos %}
    <figure class="vm-slide">
      <img src="{{ p.src }}" alt="{{ p.alt }}">
      <figcaption>{{ p.caption }}</figcaption>
    </figure>
    {% endfor %}
  </div>

  <button class="vm-next" aria-label="Next slide">›</button>
  <div class="vm-counter" aria-live="polite"></div>
</div>

<!-- ============ Notes ============ -->
<p class="fun-note">
  Outside academia, I’m usually go hiking, try different restraurants around the city, and sometimes climb rocks.
</p>
<p class="fun-note">
  Indoors, I’m into nerdy board-game nights with friends, sinking hours into ARPG/RPG video games,
  and reading sci-fi novels and mangas with my cat Curry (named after curry rice for his color).
</p>

<!-- ============ Styles (keep at bottom so overrides win) ============ -->
<style>
/* 外框：保持之前的留白 & 允许箭头超出 */
.vm-carousel{
  position:relative; margin:1rem 0; padding:0 2.5rem; outline:none; overflow:visible;
}

/* 轨道：用小 gap，方便只露出一点点 */
.vm-track{
  display:flex; gap:8px;
  overflow:hidden; transition:transform .35s ease;
  justify-content:center;                 /* 居中，左右各露一点点 */
}

/* 单张卡片：保持原来大小，只缩小一点点宽度来“露边” */
.vm-slide{
  min-width:98%;                          /* ← 调这里控制“露出”多少：99% 少露，97% 多露 */
  background:#fff; border-radius:1rem;
  box-shadow:0 6px 16px rgba(0,0,0,.12);
  overflow:hidden;
  /* 不再使用 scale 放大缩小，避免看起来“被放大” */
}

/* 图片尺寸：与你之前一致 */
.vm-slide img{
  display:block; width:100%; height:440px; object-fit:cover;
}
@media (min-width:992px){
  .vm-slide img{ height:520px; }
}

.vm-slide figcaption{
  text-align:center; font-size:.95rem; padding:.6rem 1rem; color:var(--text-muted);
}

/* 箭头按钮 */
.vm-prev,.vm-next{
  position:absolute; top:50%; transform:translateY(-50%);
  width:40px; height:40px; border:none; border-radius:50%;
  background:#fff; box-shadow:0 2px 10px rgba(0,0,0,.18);
  font-size:26px; line-height:40px; cursor:pointer; opacity:.98; z-index:10;
}
.vm-prev{left:.25rem} .vm-next{right:.25rem}
.vm-prev:hover,.vm-next:hover{opacity:1}

/* 计数器 */
.vm-counter{
  position:absolute; left:50%; bottom:.5rem; transform:translateX(-50%);
  font-size:.9rem; color:var(--text-muted);
  background:rgba(255,255,255,.9); padding:.2rem .55rem;
  border-radius:.5rem; box-shadow:0 1px 4px rgba(0,0,0,.1); z-index:9;
}
</style>

<!-- ============ Script (robust root lookup, seamless loop) ============ -->
<script>
(function () {
  /* 找到和本 <script> 配对的那一个轮播（不影响别处） */
  const root    = document.currentScript.previousElementSibling.previousElementSibling;
  const track   = root.querySelector('.vm-track');
  const prevBtn = root.querySelector('.vm-prev');
  const nextBtn = root.querySelector('.vm-next');
  const counter = root.querySelector('.vm-counter');

  /* 真实 slides（未克隆前） */
  const realSlides = Array.from(track.children);
  const N = realSlides.length;

  /* 首尾各克隆一张，实现无缝循环并让第 1 张左侧能“露出第 N 张” */
  const firstClone = realSlides[0].cloneNode(true);
  const lastClone  = realSlides[N - 1].cloneNode(true);
  track.insertBefore(lastClone, realSlides[0]);
  track.appendChild(firstClone);

  /* 现在索引范围 0..N+1，0 是 lastClone，N+1 是 firstClone；从 1 起步显示第 1 张真实图 */
  let i = 1;

  /* 每步位移 = 卡片可见宽度 + gap（像素） */
  function stepX() {
    const slideW = track.querySelector('.vm-slide').getBoundingClientRect().width;
    const gap = parseFloat(getComputedStyle(track).gap) || 0;
    return slideW + gap;
  }

  function setX(index, skipAnim = false) {
    const x = -index * stepX();
    if (skipAnim) track.style.transition = 'none';
    track.style.transform = `translateX(${x}px)`;
    if (skipAnim) { track.offsetHeight; track.style.transition = 'transform .35s ease'; }
  }

  function updateCounter() {
    const shownIdx = ((i - 1 + N) % N);     // 0..N-1
    counter.textContent = `${shownIdx + 1} / ${N}`;
  }

  function update(skipAnim=false){
    setX(i, skipAnim);
    updateCounter();
  }

  function go(dir){
    i += dir;
    update();
  }

  /* 初始定位到第 1 张真实图：此时左侧就能若隐若现看到最后一张 */
  update(true);

  /* 按钮、键盘、触摸 */
  prevBtn.addEventListener('click', () => go(-1));
  nextBtn.addEventListener('click', () => go(1));

  root.addEventListener('keydown', e=>{
    if (e.key === 'ArrowLeft')  go(-1);
    if (e.key === 'ArrowRight') go(1);
  });

  let sx = 0;
  track.addEventListener('touchstart', e => sx = e.touches[0].clientX, {passive:true});
  track.addEventListener('touchend',   e => {
    const dx = e.changedTouches[0].clientX - sx;
    if (Math.abs(dx) > 40) go(dx < 0 ? 1 : -1);
  }, {passive:true});

  /* 两端“瞬间回跳”保持无缝 */
  track.addEventListener('transitionend', ()=>{
    if (i === 0)      { i = N;   update(true); }
    else if (i === N+1){ i = 1;  update(true); }
  });

  /* 窗口尺寸变化时，重算位移避免错位 */
  window.addEventListener('resize', ()=>update(true));
})();
</script>



