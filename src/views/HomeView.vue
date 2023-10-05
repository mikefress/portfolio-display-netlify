<template>
  <section>
    <img id="logo" src="https://campaigns.cainandabelddb.com/img/logo.796b4e7a.svg" alt="cain&abelDDB logo">
    <div class="home">
      <div id="heading">
        <div id="title" ref="title">Banner1</div>

        <div id="description" ref="description">blah blah balh</div>
      </div>
      <div id="mediaHolder" ref="mediaHolder">
      </div>
    </div>
  </section>
</template>

<script>
// @ is an alias to /src
import portfolio from '@/data/portfolioItems';

import gsap from 'gsap';

export default {
  name: 'HomeView',
  components: {
  },
  data() {
    return {
      counter: 0,
      mediaPlayer: null,
    };
  },
  methods: {
    checkAndSetMediaPlayer(item) {
      if (item.type === 'Banner') {
        this.mediaPlayer = document.createElement('iframe');
        this.mediaPlayer.setAttribute('src', item.source);
        this.mediaPlayer.setAttribute('height', item.height);
        this.mediaPlayer.setAttribute('width', item.width);
        this.mediaPlayer.setAttribute('frameBorder', 0);
        this.mediaPlayer.setAttribute('id', 'mediaPlayer');
        this.$refs.mediaHolder.style.marginTop = item.heightOffset;
        this.$refs.mediaHolder.appendChild(this.mediaPlayer);
      }
      if (item.type === 'Video') {
        // alert('boo!');
        this.mediaPlayer = document.createElement('video');
        this.mediaPlayer.setAttribute('src', item.source);
        this.mediaPlayer.setAttribute('height', item.height);
        this.mediaPlayer.setAttribute('width', item.width);
        this.mediaPlayer.setAttribute('id', 'mediaPlayer');
        // this.mediaPlayer.setAttribute('muted', true);
        // this.mediaPlayer.setAttribute('autoplay', true);
        this.mediaPlayer.muted = true;
        this.mediaPlayer.autoplay = true;
        this.$refs.mediaHolder.style.marginTop = item.heightOffset;
        this.$refs.mediaHolder.appendChild(this.mediaPlayer);
      }
    },
    async slideTimeline(item) {
      // eslint-disable-next-line new-cap
      const subTL = new gsap.timeline();
      subTL.to('.home', { autoAlpha: 0, duration: 0.5, ease: 'power1.easeOut' }, 0);
      subTL.call(() => {
        if (this.mediaPlayer) this.mediaPlayer.remove();
        this.mediaPlayer = null;
      }, null, '+=0');
      subTL.call(() => {
        this.$refs.title.innerHTML = item.name;
        this.$refs.description.innerHTML = item.description;
        this.checkAndSetMediaPlayer(item);
      }, null, '+=0');
      subTL.to('.home', { autoAlpha: 1, duration: 0.5, ease: 'power1.easeOut' }, '+=0');
      subTL.addLabel('nextSlide', item.time + 2);
      subTL.call(() => {
        console.log('complete');
      }, null, 'nextSlide');
    },
    async masterLoop() {
      this.slideTimeline(portfolio[this.counter]);
      setTimeout(() => {
        this.incrementCounterAndGoAgain();
      }, (portfolio[this.counter].time + 3) * 1000);
    },
    async incrementCounterAndGoAgain() {
      this.counter += 1;
      if (this.counter > portfolio.length - 1) this.counter = 0;
      await this.masterLoop();
    },
  },
  mounted() {
    this.masterLoop();
  },
};
</script>

<style scoped>
.home {
  display: flex;
  flex-direction: column;
  max-width: 1200px;
  margin: 0 auto;
  opacity: 0;
}

#logo {
  position: absolute;
  bottom: 1vh;
  right: 1vw;
  width: 100px;
  height: auto;
}

#heading {
  max-width: 1200px;
  text-align: left;
  display: flex;
  flex-direction: column;
  margin-top: 2rem;
  margin-bottom: 2rem;
}

#title {
  font-size: 40px;
  line-height: 1.2;
  letter-spacing: 0.1em;
  font-weight: 600;
  margin-bottom: 1rem;
}

#description {
  font-size: 24px;
}

#mediaHolder {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

</style>
