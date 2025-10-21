---
layout: page
title: Fun stuff
permalink: /fun-stuff/
description: Fun stuff outside academia
nav: true            # ← 让它出现在导航栏
nav_order: 80        # ← 排序：数字越小越靠左（按需改，比如 60/70/90）
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
  Outside academia, I’m usually out hiking, restaurant-hopping around the city, and sometimes climbing rocks.
  I also love cuddling my cat <strong>Curry</strong> (named after curry rice for his color).
</p>
<p class="fun-note">
  Indoors, I’m into nerdy board-game nights with friends, sinking hours into <strong>ARPG/RPG</strong> video games,
  and reading sci-fi novels and manga.
</p>

<!-- ============ Styles (keep at bottom so overrides win) ============ -->
<style>
/* layout */
.vm-carousel{position:relative;margin:1rem 0;padding:0 2.5rem;overflow:visible}
.vm-track{display:flex;gap:0;overflow:visible;transition:transform .35s ease}
.vm-slide{min-width:100%;background:#fff;border-radius:1rem;box-shadow:0 6px 16px rgba(0,0,0,.12);overflow:hidden}
.vm-slide img{display:block;width:100%;height:440px;object-fit:cover}
.vm-slide figcaption{text-align:center;font-size:.95rem;padding:.6rem 1rem;color:var(--text-muted)}
/* controls */
.vm-prev,.vm-next{
  position:absolute;top:50%;transform:translateY(-50%);
  width:38px;height:38px;border:none;border-radius:50%;
  background:#fff;box-shadow:0 2px 8px rgba(0,0,0,.18);
  font-size:26px;line-height:38px;cursor:pointer;opacity:.95;z-index:999
}
.vm-prev{left:.75rem}.vm-next{right:.75rem}
.vm-prev:hover,.vm-next:hover{opacity:1}
.vm-counter{
  position:absolute;left:50%;bottom:.5rem;transform:translateX(-50%);
  font-size:.9rem;color:var(--text-muted);
  background:rgba(255,255,255,.85);padding:.2rem .55rem;border-radius:.5rem;box-shadow:0 1px 4px rgba(0,0,0,.1);z-index:9
}
/* text */
.fun-note{max-width:900px;margin:1rem auto;line-height:1.85;color:var(--text-muted)}
@media (min-width:992px){ .vm-slide img{height:520px} }
</style>

<!-- ============ Script (robust root lookup, no lazy pitfalls) ============ -->
<script>
(function(){
  const root = document.getElementById('funCarousel');
  if (!root) return;
  const track = root.querySelector('.vm-track');
  const slides = Array.from(root.querySelectorAll('.vm-slide'));
  const prev = root.querySelector('.vm-prev');
  const next = root.querySelector('.vm-next');
  const counter = root.querySelector('.vm-counter');
  let i = 0;

  function update(){
    track.style.transform = 'translateX(' + (-i*100) + '%)';
    if (counter) counter.textContent = (i+1) + ' / ' + slides.length;
  }
  function go(n){
    if (!slides.length) return;
    i = (n + slides.length) % slides.length;
    update();
  }

  prev.addEventListener('click', ()=>go(i-1));
  next.addEventListener('click', ()=>go(i+1));
  root.addEventListener('keydown', e=>{
    if(e.key==='ArrowLeft') go(i-1);
    if(e.key==='ArrowRight') go(i+1);
  });

  // swipe
  let sx=0;
  track.addEventListener('touchstart',e=>sx=e.touches[0].clientX,{passive:true});
  track.addEventListener('touchend',e=>{
    const dx=e.changedTouches[0].clientX - sx;
    if(Math.abs(dx)>40) go(dx<0? i+1 : i-1);
  },{passive:true});

  update();
})();
</script>

