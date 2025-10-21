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
.vm-carousel{position:relative;margin:1rem 0;padding:0 3rem;outline:none; overflow:visible}
.vm-track{
  display:flex; gap:16px;            /* gap 用 px 更好算 */
  overflow:hidden; transition:transform .35s ease;
  justify-content:center;            /* 居中，左右都能露一点 */
}
.vm-slide{
  min-width: 90%;                    /* 90%~94% 都好看，自己调 */
  background:#fff;border-radius:1rem;
  box-shadow:0 6px 16px rgba(0,0,0,.12);overflow:hidden;
  transform: scale(.98); opacity:.95; transition:transform .25s ease, opacity .25s ease;
}
.vm-slide.is-active{ transform:scale(1); opacity:1; }
.vm-slide img{display:block;width:100%;height:460px;object-fit:cover}
.vm-slide figcaption{text-align:center;font-size:.95rem;padding:.6rem 1rem;color:var(--text-muted)}

.vm-prev,.vm-next{
  position:absolute;top:50%;transform:translateY(-50%);
  width:40px;height:40px;border:none;border-radius:50%;
  background:#fff;box-shadow:0 2px 10px rgba(0,0,0,.18);
  font-size:26px;line-height:40px;cursor:pointer;opacity:.98; z-index:10;
}
.vm-prev{left:.25rem}.vm-next{right:.25rem}
.vm-prev:hover,.vm-next:hover{opacity:1}

.vm-counter{
  position:absolute;left:50%;bottom:.5rem;transform:translateX(-50%);
  font-size:.9rem;color:var(--text-muted);
  background:rgba(255,255,255,.9);padding:.2rem .55rem;border-radius:.5rem;box-shadow:0 1px 4px rgba(0,0,0,.1); z-index:9;
}
@media (min-width:992px){ .vm-slide img{height:520px} }


<!-- ============ Script (robust root lookup, no lazy pitfalls) ============ -->
<script>
(function () {
  const root   = document.currentScript.previousElementSibling.previousElementSibling;
  const track  = root.querySelector('.vm-track');
  const prev   = root.querySelector('.vm-prev');
  const next   = root.querySelector('.vm-next');
  const counter= root.querySelector('.vm-counter');

  // 收集真实 slides
  const realSlides = Array.from(track.children);
  const N = realSlides.length;

  // 前后各克隆一张，实现无缝循环
  const firstClone = realSlides[0].cloneNode(true);
  const lastClone  = realSlides[N-1].cloneNode(true);
  track.insertBefore(lastClone, realSlides[0]);
  track.appendChild(firstClone);

  // 现在索引范围 0..N+1（0 是 lastClone，N+1 是 firstClone）
  let i = 1;

  // 计算每步位移（= 卡片可见宽度 + gap 像素）
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

  function setActive() {
    const slides = track.querySelectorAll('.vm-slide');
    slides.forEach(s => s.classList.remove('is-active'));
    // 显示中的真实位置
    const shownIdx = ((i - 1 + N) % N); // 0..N-1
    // 克隆在两端，真正中间的第 i 张对应 track.children[i]
    // 找到与 realSlides[shownIdx] 相同的那一张（中间那组）
    const mid = track.children[i];
    if (mid) mid.classList.add('is-active');
    counter.textContent = `${shownIdx + 1} / ${N}`;
  }

  function update(skipAnim=false){ setX(i, skipAnim); setActive(); }

  function go(dir){ i += dir; update(); }

  // 初始定位到第 1 张真实图，让左侧能露出最后一张
  update(true);

  prev.addEventListener('click', ()=>go(-1));
  next.addEventListener('click', ()=>go(1));

  // 两端回跳（无动画）保持无缝
  track.addEventListener('transitionend', ()=>{
    if (i === 0)      { i = N;   update(true); }
    else if (i === N+1){ i = 1;   update(true); }
  });

  // 键盘
  root.addEventListener('keydown', e=>{
    if (e.key==='ArrowLeft')  go(-1);
    if (e.key==='ArrowRight') go(1);
  });

  // 触摸
  let sx=0;
  track.addEventListener('touchstart', e=>sx=e.touches[0].clientX, {passive:true});
  track.addEventListener('touchend',   e=>{
    const dx = e.changedTouches[0].clientX - sx;
    if (Math.abs(dx)>40) go(dx<0? 1 : -1);
  }, {passive:true});

  // 视口变化时重新对齐（避免尺寸变化后位移不准）
  window.addEventListener('resize', ()=>update(true));
})();
</script>


