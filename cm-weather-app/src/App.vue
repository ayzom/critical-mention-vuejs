<template>
<div class="bg">
  <Weather/>
</div>
</template>

<script>
import Weather from './components/Weather';

export default {
  name: 'App',

  components: {
    Weather,
  },

  data: () => ({
    //
  }),
};
</script>

<style>
.bg {
  --bg: #d9e4f5;
  --img: linear-gradient(315deg, #d9e4f5 0%, #f5e3e6 74%);
  --img: linear-gradient(320deg, #85a3ce 0%, #f5dadf 74%);
  --color: #000;
    position: relative;
    background: var(--bg);
    color: var(--color);
    background-image: var(--img);
    min-height: 100vh;
    width: 100vw;
    border-top-left-radius: 30px;
    top: 0;
    left: 0;
    -webkit-transition: all 0.2s linear 0s;
    transition: all 0.2s linear 0s; 
}
</style>