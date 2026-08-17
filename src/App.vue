<script setup lang="ts">
import { computed, onBeforeUnmount, onMounted, ref } from 'vue'

const opened = ref(false), loading = ref(true), letterOpen = ref(false), candlesOut = ref(false), musicOn = ref(false), confetti = ref(false)
const progress = ref(0), lightbox = ref<number | null>(null), cursor = ref({ x: -100, y: -100 })
let audio: AudioContext | null = null, timer: number | undefined
const reasons = ['Your smile.','Your kindness.','Your beautiful heart.','The way you make ordinary moments memorable.','Your energy.','Your quiet strength.','The warmth you bring into every room.','Your contagious laugh.','How deeply you care.','Your honest soul.','The little things you remember.','Your courage.','The comfort of your presence.','Your brilliant mind.','The dreams you never stop chasing.','Your sense of wonder.','How you make people feel seen.','Your effortless elegance.','The joy you give so freely.','Your one-of-a-kind spirit.','Every memory still waiting to be made.','Simply because you are Morha.']
const memories = [
  {src:'https://images.unsplash.com/photo-1496440737103-cd596325d314?auto=format&fit=crop&w=900&q=85',caption:'A day worth remembering',date:'Our story · 01'},
  {src:'https://images.unsplash.com/photo-1529139574466-a303027c1d8b?auto=format&fit=crop&w=900&q=85',caption:'That beautiful laugh',date:'Our story · 02'},
  {src:'https://images.unsplash.com/photo-1507501336603-6e31db2be093?auto=format&fit=crop&w=900&q=85',caption:'Golden little moments',date:'Our story · 03'},
  {src:'https://images.unsplash.com/photo-1524250502761-1ac6f2e30d43?auto=format&fit=crop&w=900&q=85',caption:'One for the memory book',date:'Our story · 04'},
  {src:'https://images.unsplash.com/photo-1524504388940-b1c1722653e1?auto=format&fit=crop&w=900&q=85',caption:'Somewhere, together',date:'Our story · 05'},
  {src:'https://images.unsplash.com/photo-1488426862026-3ee34a7d66df?auto=format&fit=crop&w=900&q=85',caption:'A moment that stayed',date:'Our story · 06'}]
const particles=Array.from({length:36},(_,i)=>({left:`${i*37%100}%`,delay:`${i%12*.6}s`,duration:`${7+i%7}s`,size:`${2+i%3}px`}))
const pieces=Array.from({length:44},(_,i)=>({left:`${i*43%100}%`,delay:`${i%11*.08}s`,color:['#f5b8c8','#e8c78e','#fff1f4','#c88fa6'][i%4]}))
const chapterLabel=computed(()=>progress.value<8?'The beginning':progress.value>92?'Chapter 22':`${Math.round(progress.value)}%`)
const selectedMemory=computed(()=>lightbox.value===null?null:memories[lightbox.value])
function burst(){confetti.value=false;requestAnimationFrame(()=>{confetti.value=true;setTimeout(()=>confetti.value=false,3500)})}
function enterGift(){burst();opened.value=true;setTimeout(()=>document.querySelector('#chapter')?.scrollIntoView({behavior:'smooth'}),450)}
function blowCandles(){if(!candlesOut.value){candlesOut.value=true;burst()}}
function toggleMusic(){musicOn.value=!musicOn.value;if(!musicOn.value){audio?.close();audio=null;return}audio=new AudioContext();[261.63,329.63,392,493.88].forEach((f,i)=>{const o=audio!.createOscillator(),g=audio!.createGain();o.type='sine';o.frequency.value=f/2;g.gain.value=.018;o.connect(g);g.connect(audio!.destination);o.start(audio!.currentTime+i*.12)})}
function onScroll(){const max=document.documentElement.scrollHeight-innerHeight;progress.value=max>0?scrollY/max*100:0}
function onMove(e:MouseEvent){cursor.value={x:e.clientX,y:e.clientY}}
function onKey(e:KeyboardEvent){if(e.key==='Escape')lightbox.value=null}
onMounted(()=>{timer=setTimeout(()=>loading.value=false,1800);addEventListener('scroll',onScroll,{passive:true});addEventListener('mousemove',onMove);addEventListener('keydown',onKey)})
onBeforeUnmount(()=>{clearTimeout(timer);removeEventListener('scroll',onScroll);removeEventListener('mousemove',onMove);removeEventListener('keydown',onKey);audio?.close()})
</script>

<template>
  <Transition name="loader"><div v-if="loading" class="loader"><div class="loader-mark">M</div><p>Preparing something special for Morha…</p><span /></div></Transition>
  <div class="cursor-glow" :style="{transform:`translate(${cursor.x}px,${cursor.y}px)`}" />
  <div class="progress"><span :style="{width:progress+'%'}"/><small>{{chapterLabel}}</small></div>
  <button class="music" :class="{playing:musicOn}" @click="toggleMusic" :aria-label="musicOn?'Pause ambient music':'Play ambient music'"><i v-for="n in 3" :key="n"/>{{musicOn?'Pause':'Music'}}</button>
  <div class="particles" aria-hidden="true"><i v-for="(p,i) in particles" :key="i" :style="{left:p.left,animationDelay:p.delay,animationDuration:p.duration,width:p.size,height:p.size}"/></div>
  <div v-if="confetti" class="confetti" aria-hidden="true"><i v-for="(p,i) in pieces" :key="i" :style="{left:p.left,animationDelay:p.delay,background:p.color}"/></div>
  <main>
    <section class="hero" :class="{opened}"><div class="hero-orbit"><span>22</span></div><div class="hero-copy"><p class="eyebrow">A little something, made just for you</p><h1><span class="mystery">Someone very special just turned 22…</span><span class="reveal">Happy Birthday,<em> Morha</em> <b>♡</b></span></h1><p class="subtitle">22 years of making the world a little more beautiful.</p><button class="primary" @click="enterGift">Open Your Birthday Gift <span>✦</span></button></div><div class="scroll-hint">A story in six moments <i/></div></section>
    <div v-show="opened" class="journey">
      <section id="chapter" class="chapter section"><div class="giant-22">22</div><p class="eyebrow">The next page begins</p><h2>Welcome to <em>Chapter 22,</em><br>Morha.</h2><p class="lead">A new year. New memories. New adventures.<br>And hopefully, your best chapter yet.</p><div class="chapter-note"><span>Twenty-two</span><i/>A year to become even more you.</div></section>
      <section class="reasons section"><header class="section-head"><p class="eyebrow">A list that could go on forever</p><h2>22 Reasons Why<br><em>Morha Is Special</em></h2><p>Tap each card to uncover a little truth.</p></header><div class="reasons-grid"><button v-for="(reason,i) in reasons" :key="reason" class="reason-card" @click="($event.currentTarget as HTMLElement).classList.toggle('flipped')"><span class="front"><b>{{String(i+1).padStart(2,'0')}}</b><i>✦</i></span><span class="back">{{reason}}</span></button></div></section>
      <section class="memories section"><header class="section-head"><p class="eyebrow">Collected pieces of time</p><h2>Some Moments Deserve<br>to <em>Stay Forever.</em></h2></header><div class="gallery"><button v-for="(photo,i) in memories" :key="photo.src" class="polaroid" :class="`p${i+1}`" @click="lightbox=i"><img :src="photo.src" :alt="photo.caption" loading="lazy"><span><b>{{photo.caption}}</b><small>{{photo.date}}</small></span></button></div><p class="replace-note">Six spaces, waiting for your favorite memories together.</p></section>
      <section class="letter-section section"><div class="letter-intro"><p class="eyebrow">Words meant only for you</p><h2>There’s something<br>I wanted to <em>tell you…</em></h2><p>Some feelings deserve more than a message on a screen.</p></div><div class="envelope-wrap" :class="{open:letterOpen}"><div class="letter"><p class="letter-date">On your 22nd birthday</p><h3>Dear Morha,</h3><p>Today is a celebration of you — of every beautiful thing you are, and every wonderful thing you are still becoming.</p><p>This is your space for a heartfelt birthday message. Replace these words with your own memories, hopes, and everything you have always wanted Morha to know.</p><p>May chapter 22 bring you closer to every dream your heart has been quietly holding.</p><p class="signature">Always,<br><em>Your Name</em></p></div><div class="envelope"><div class="flap"/><div class="pocket"/><div class="seal">M</div></div></div><button class="primary light" @click="letterOpen=!letterOpen">{{letterOpen?'Close the Letter':'Open the Letter 💌'}}</button></section>
      <section class="wish section"><div class="wish-copy"><p class="eyebrow">One more little ritual</p><h2>Before you continue…</h2><p>Close your eyes and make a wish.</p></div><div class="cake" :class="{blown:candlesOut}"><div class="candles"><i v-for="n in 22" :key="n"><span/></i></div><div class="cake-top"/><div class="cake-body"><span>make a wish</span></div><div class="cake-plate"/></div><button class="primary" @click="blowCandles" :disabled="candlesOut">{{candlesOut?'Your wish is on its way ✨':'Blow the Candles 🎂'}}</button><Transition name="wish"><p v-if="candlesOut" class="wish-message">I hope every wish you just made comes true.</p></Transition></section>
      <section class="finale section"><div class="rings"><i/><i/><i/></div><p class="eyebrow">The beginning, not the end</p><h2>Happy 22nd Birthday,<br><em>Morha.</em></h2><p>This year is yours. Make it unforgettable.</p><div class="final-line"><i/>Chapter 22 starts now. ✦<i/></div><span class="with-love">made with love, especially for you</span></section>
    </div>
  </main>
  <Transition name="lightbox"><div v-if="selectedMemory" class="lightbox" role="dialog" aria-modal="true" @click.self="lightbox=null"><button @click="lightbox=null" aria-label="Close photo">×</button><img :src="selectedMemory.src" :alt="selectedMemory.caption"><p>{{selectedMemory.caption}} <small>{{selectedMemory.date}}</small></p></div></Transition>
</template>
