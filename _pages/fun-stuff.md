---
layout: page
title: Fun stuff
permalink: /fun-stuff/
description: Fun stuff outside academia
---

<div class="vm-carousel" tabindex="0">
  <button class="vm-prev" aria-label="Previous slide">‹</button>

  <div class="vm-track">
    {% for p in site.data.fun_photos %}
    <figure class="vm-slide">
      <img src="{{ p.src }}" alt="{{ p.alt }}" loading="{% if forloop.first %}eager{% else %}lazy{% endif %}">
      <figcaption>{{ p.caption }}</figcaption>
    </figure>
    {% endfor %}
  </div>

  <button class="vm-next" aria-label="Next slide">›</button>
  <div class="vm-counter" aria-live="polite"></div>
</div>

<style>
.vm-carousel{position:relative;margin:1rem 0;padding:0 2.5rem;outline:none}
.vm-track{display:flex;gap:1rem;overflow:hidden;transition:transform .35s ease}
.vm-slide{min-width:100%;background:#fff;border-radius:1rem;box-shadow:0 6px 16px rgba(0,0,0,.12);overflow:hidden}
.vm-slide img{display:block;width:100%;height:440px;object-fit:cover}
.vm-slide figcaption{text-align:center;font-size:.95rem;padding:.6rem 1rem;color:var(--text-muted)}
.vm-prev,.vm-next{
  position:absolute;top:50%;transform:translateY(-50%);
  width:38px;height:38px;border:none;border-radius:50%;
  background:#fff;box-shadow:0 2px 8px rgba(0,0,0,.18);
  font-size:26px;line-height:38px;cursor:pointer;opacity:.95
}
.vm-prev{left:.25rem}.vm-next{right:.25rem}
.vm-prev:hover,.vm-next:hover{opacity:1}
.vm-counter{
  position:absolute;left:50%;bottom:.5rem;transform:translateX(-50%);
  font-size:.9rem;color:var(--text-muted);
  background:rgba(255,255,255,.85);padding:.2rem .55rem;border-radius:.5rem;box-shadow:0 1px 4px rgba(0,0,0,.1)
}
.vm-prev, .vm-next { z-index: 10; }
.vm-counter { z-index: 9; }
.vm-carousel { overflow: visible; }
@media (min-width:992px){ .vm-slide img{height:520px} }
</style>

<script>
(function(){
  const root = document.currentScript.previousElementSibling.previousElementSibling;
  const track = root.querySelector('.vm-track');
  const slides = Array.from(root.querySelectorAll('.vm-slide'));
  const prev = root.querySelector('.vm-prev');
  const next = root.querySelector('.vm-next');
  const counter = root.querySelector('.vm-counter');
  let i = 0, autoTimer = null;

  function update(){
    track.style.transform = `translateX(${-i*100}%)`;
    counter.textContent = `${i+1} / ${slides.length}`;
  }
  function go(n){ i = (n + slides.length) % slides.length; update(); }

  // 按钮、键盘、触摸
  prev.addEventListener('click', () => go(i-1));
  next.addEventListener('click', () => go(i+1));
  root.addEventListener('keydown', e=>{
    if(e.key==='ArrowLeft') go(i-1);
    if(e.key==='ArrowRight') go(i+1);
  });
  let sx=0; track.addEventListener('touchstart',e=>sx=e.touches[0].clientX,{passive:true});
  track.addEventListener('touchend',e=>{
    const dx=e.changedTouches[0].clientX - sx;
    if(Math.abs(dx)>40) go(dx<0? i+1 : i-1);
  }, {passive:true});

  // （可选）自动播放：取消下一行注释即可，间隔 4 秒
  // autoTimer = setInterval(()=>go(i+1), 4000);
  // 鼠标悬停时暂停
  // root.addEventListener('mouseenter', ()=>clearInterval(autoTimer));
  // root.addEventListener('mouseleave', ()=>autoTimer=setInterval(()=>go(i+1), 4000));

  update();
})();
</script>

<p class="fun-note">
Outside academia, I’m usually out hiking, restaurant-hopping around the city, and sometimes climbing rocks.
I also love cuddling my cat <strong>Curry</strong> (named after curry rice for his color).
</p>

<p class="fun-note">
Indoors, I’m into nerdy board-game nights with friends, sinking hours into <strong>ARPG/RPG</strong> video games,
and reading sci-fi novels and manga.
</p>

<style>
.fun-note{
  max-width: 900px;
  margin: 1rem auto;
  line-height: 1.85;
  color: var(--text-muted);
}
</style>

