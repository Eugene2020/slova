<template lang="pug">
#app
    img(:src='activityImage')
    h1 Собери слово
    Playground(:winWord='correctWord' :playWord='mixedWord' @oneMoreWord="getCorrectActivity()")
    p {{ activityInfo }}
</template>

<script>
import axios from 'axios';
import Playground from './components/Playground.vue';

export default {
  data() {
    return {
      activityInfo: null,
      activityImage: null,
      correctActivity: null,
      arWord: [],
      correctWord: [],
      mixedWord: null,
    };
  },
  methods: {
    randNum(min, max) {
      return Math.floor(min + Math.random() * (max + 1 - min));
    },
    getActivity() {
      const num = this.randNum(2, 1368);
      return axios.get(`https://apidir.pfdo.ru/v1/directory-program-activities/${num}`);
    },
    makeMixedWord() {
      const arr = this.arWord;
      for (let i = arr.length - 1; i > 0; i -= 1) {
        const j = Math.floor(Math.random() * (i + 1));
        [arr[i], arr[j]] = [arr[j], arr[i]];
      }
      this.mixedWord = arr;
      return this.mixedWord;
    },
    getCorrectActivity() {
      this.getActivity().then((response) => {
        if (response.data.data && response.data.data.status === 10) {
          let activityName = response.data.data.name;
          activityName = activityName.toUpperCase();
          this.correctActivity = activityName;
          this.arWord = activityName.split('');
          this.correctWord = activityName.split('');
          this.makeMixedWord();
        } else {
          this.getCorrectActivity();
        }
      });
    },
  },
  mounted() {
    this.getCorrectActivity();
    this.makeMixedWord();
  },
  name: 'app',
  components: {
    Playground,
  },
};
</script>

<style lang="sass">
#app
  font-family: 'Avenir', Helvetica, Arial, sans-serif
  -webkit-font-smoothing: antialiased
  -moz-osx-font-smoothing: grayscale
  text-align: center
  color: #2c3e50
  margin-top: 60px
</style>
