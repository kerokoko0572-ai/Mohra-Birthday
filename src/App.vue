<script setup lang="ts">
import { computed, onBeforeUnmount, onMounted, ref } from 'vue'

const opened = ref(false), loading = ref(true), letterOpen = ref(false), candlesOut = ref(false), musicOn = ref(false), confetti = ref(false)
const progress = ref(0), lightbox = ref<number | null>(null), cursor = ref({ x: -100, y: -100 })
const audioEl = ref<HTMLAudioElement | null>(null)
let timer: number | undefined
const reasons = ['Your smile.','Your kindness.','Your beautiful heart.','The way you make ordinary moments memorable.','Your energy.','Your quiet strength.','The warmth you bring into every room.','Your contagious laugh.','How deeply you care.','Your honest soul.','The little things you remember.','Your courage.','The comfort of your presence.','Your brilliant mind.','The dreams you never stop chasing.','Your sense of wonder.','How you make people feel seen.','Your effortless elegance.','The joy you give so freely.','Your one-of-a-kind spirit.','Every memory still waiting to be made.','Simply because you are Maro.']
const memories = [
  {src:'/maro-portrait.jpg',caption:'The smile that lights up every moment',date:'Maro · 22'},
  {src:'/maro-paris.png',caption:'Paris looked better with you in it',date:'A Paris memory'},
  {src:'/maro-birthday-22.png',caption:'The moment chapter 22 began',date:'Birthday · 22'},
  {src:'/maro-cake-22.png',caption:'A wish made especially for you',date:'Twenty-two wishes'},
  {src:'/maro-garden.png',caption:'Blooming wherever life takes you',date:'A beautiful day'},
  {src:'/maro-blue-evening.png',caption:'Elegance in every shade',date:'An evening to remember'},
  {src:'/maro-wish-22.png',caption:'One candle closer to every dream',date:'Chapter 22'}]
const particles=Array.from({length:36},(_,i)=>({left:`${i*37%100}%`,delay:`${i%12*.6}s`,duration:`${7+i%7}s`,size:`${2+i%3}px`}))
const pieces=Array.from({length:44},(_,i)=>({left:`${i*43%100}%`,delay:`${i%11*.08}s`,color:['#f5b8c8','#e8c78e','#fff1f4','#c88fa6'][i%4]}))
const chapterLabel=computed(()=>progress.value<8?'The beginning':progress.value>92?'Chapter 22':`${Math.round(progress.value)}%`)
const selectedMemory=computed(()=>lightbox.value===null?null:memories[lightbox.value])
function burst(){confetti.value=false;requestAnimationFrame(()=>{confetti.value=true;setTimeout(()=>confetti.value=false,3500)})}
async function enterGift(){burst();opened.value=true;if(audioEl.value&&!musicOn.value){try{await audioEl.value.play();musicOn.value=true}catch{musicOn.value=false}}setTimeout(()=>document.querySelector('#chapter')?.scrollIntoView({behavior:'smooth'}),450)}
function blowCandles(){if(!candlesOut.value){candlesOut.value=true;burst()}}
async function toggleMusic(){if(!audioEl.value)return;if(musicOn.value){audioEl.value.pause();musicOn.value=false;return}try{await audioEl.value.play();musicOn.value=true}catch{musicOn.value=false}}
function onScroll(){const max=document.documentElement.scrollHeight-innerHeight;progress.value=max>0?scrollY/max*100:0}
function onMove(e:MouseEvent){cursor.value={x:e.clientX,y:e.clientY}}
function onKey(e:KeyboardEvent){if(e.key==='Escape')lightbox.value=null}
onMounted(()=>{timer=setTimeout(()=>loading.value=false,1800);addEventListener('scroll',onScroll,{passive:true});addEventListener('mousemove',onMove);addEventListener('keydown',onKey)})
onBeforeUnmount(()=>{clearTimeout(timer);removeEventListener('scroll',onScroll);removeEventListener('mousemove',onMove);removeEventListener('keydown',onKey);audioEl.value?.pause()})
</script>

<template>
  <Transition name="loader"><div v-if="loading" class="loader"><div class="loader-mark">M</div><p>Preparing something special for Maro…</p><span /></div></Transition>
  <audio ref="audioEl" src="/ana-le-habiby.mp3" loop preload="metadata" @ended="musicOn=false" />
  <div class="cursor-glow" :style="{transform:`translate(${cursor.x}px,${cursor.y}px)`}" />
  <div class="progress"><span :style="{width:progress+'%'}"/><small>{{chapterLabel}}</small></div>
  <button class="music" :class="{playing:musicOn}" @click="toggleMusic" :aria-label="musicOn?'Pause ambient music':'Play ambient music'"><i v-for="n in 3" :key="n"/>{{musicOn?'Pause':'Music'}}</button>
  <div class="particles" aria-hidden="true"><i v-for="(p,i) in particles" :key="i" :style="{left:p.left,animationDelay:p.delay,animationDuration:p.duration,width:p.size,height:p.size}"/></div>
  <div v-if="confetti" class="confetti" aria-hidden="true"><i v-for="(p,i) in pieces" :key="i" :style="{left:p.left,animationDelay:p.delay,background:p.color}"/></div>
  <main>
    <section class="hero" :class="{opened}"><div class="hero-orbit"><span>22</span></div><div class="hero-copy"><p class="eyebrow">A little something, made just for you</p><h1><span class="mystery">Someone very special just turned 22…</span><span class="reveal">Happy Birthday,<em> Maro</em> <b>♡</b></span></h1><p class="subtitle">22 years of making the world a little more beautiful.</p><button class="primary" @click="enterGift">Open Your Birthday Gift <span>✦</span></button></div><div class="scroll-hint">A story in six moments <i/></div></section>
    <div v-show="opened" class="journey">
      <section id="chapter" class="chapter section"><div class="giant-22">22</div><p class="eyebrow">The next page begins</p><h2>Welcome to <em>Chapter 22,</em><br>Maro.</h2><p class="lead">A new year. New memories. New adventures.<br>And hopefully, your best chapter yet.</p><div class="chapter-note"><span>Twenty-two</span><i/>A year to become even more you.</div></section>
      <section class="reasons section"><header class="section-head"><p class="eyebrow">A list that could go on forever</p><h2>22 Reasons Why<br><em>Maro Is Special</em></h2><p>Hover or tap each card to uncover a little truth.</p></header><div class="reasons-grid"><button v-for="(reason,i) in reasons" :key="reason" class="reason-card" @click="($event.currentTarget as HTMLElement).classList.toggle('flipped')"><span class="front"><b>{{String(i+1).padStart(2,'0')}}</b><i>✦</i></span><span class="back">{{reason}}</span></button></div></section>
      <section class="memories section"><header class="section-head"><p class="eyebrow">Collected pieces of time</p><h2>Some Moments Deserve<br>to <em>Stay Forever.</em></h2></header><div class="gallery"><button v-for="(photo,i) in memories" :key="photo.src" class="polaroid" :class="`p${i+1}`" @click="lightbox=i"><img :src="photo.src" :alt="photo.caption" loading="lazy"><span><b>{{photo.caption}}</b><small>{{photo.date}}</small></span></button></div><p class="replace-note">Seven little windows into a beautiful story.</p></section>
      <section class="letter-section section"><div class="letter-intro"><p class="eyebrow">Words meant only for you</p><h2>There’s something<br>I wanted to <em>tell you…</em></h2><p>Some feelings deserve more than a message on a screen.</p></div><div class="envelope-wrap" :class="{open:letterOpen}"><div class="letter"><p class="letter-date">On your 22nd birthday</p><h3>Dear Maro,</h3><p>Happy 22nd Birthday to one of the most special people in my life. 🤍</p><p>I genuinely hope this year brings you everything you've been wishing for, even the things you don't always talk about.</p><p>Thank you for every laugh, every random conversation, every memory, and all those little moments that somehow became some of my favorites.</p><p>I'm really grateful to have you in my life, and I hope no matter where life takes us, we'll always have more memories to make, more things to laugh about, and more stories to look back on.</p><p>You deserve a year full of happiness, success, peace, and everything you've been working for.</p><p>So here's to <strong>22</strong>, Maro. 🥂<br>A new chapter, new memories, and hopefully your best year yet.</p><p class="signature"><strong>Happy Birthday, bestie. ❤️</strong></p></div><div class="envelope"><div class="flap"/><div class="pocket"/><div class="seal">M</div></div></div><button class="primary light" @click="letterOpen=!letterOpen">{{letterOpen?'Close the Letter':'Open the Letter 💌'}}</button></section>
      <section class="wish section"><div class="wish-copy"><p class="eyebrow">One more little ritual</p><h2>Before you continue…</h2><p>Close your eyes and make a wish.</p></div><div class="cake" :class="{blown:candlesOut}"><div class="candles"><i v-for="n in 22" :key="n"><span/></i></div><div class="cake-top"/><div class="cake-body"><span>make a wish</span></div><div class="cake-plate"/></div><button class="primary" @click="blowCandles" :disabled="candlesOut">{{candlesOut?'Your wish is on its way ✨':'Blow the Candles 🎂'}}</button><Transition name="wish"><p v-if="candlesOut" class="wish-message">I hope every wish you just made comes true.</p></Transition></section>
      <section class="finale section"><div class="rings"><i/><i/><i/></div><p class="eyebrow">The beginning, not the end</p><h2>Happy 22nd Birthday,<br><em>Maro.</em></h2><p>This year is yours. Make it unforgettable.</p><div class="final-line"><i/>Chapter 22 starts now. ✦<i/></div><span class="with-love">made with love, especially for you</span></section>
    </div>
  </main>
  <Transition name="lightbox"><div v-if="selectedMemory" class="lightbox" role="dialog" aria-modal="true" @click.self="lightbox=null"><button @click="lightbox=null" aria-label="Close photo">×</button><img :src="selectedMemory.src" :alt="selectedMemory.caption"><p>{{selectedMemory.caption}} <small>{{selectedMemory.date}}</small></p></div></Transition>
</template>
