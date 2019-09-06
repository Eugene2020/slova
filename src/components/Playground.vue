<template lang="pug">
.game
  div(v-if='isWin === false')
    ul
      li(v-for='(letter, index) in playWord'
      v-on:click='enteredWord.push(letter);hiddenLetters.push(index);checkWin();'
      v-if='hiddenLetters.indexOf(index) === -1')
        span {{ letter }}
    ul.enterField
      li(v-for='(letter, index) in enteredWord')
        span {{ letter }}
    a(v-on:click='newGame();') Сбросить
  div(v-if='isWin === true && needResult === false')
    p Ты выиграл! Повторить?
    a(v-on:click='$emit("oneMoreWord");isWin = false;newGame();') Конечно!
    br
    a(v-on:click='needResult = true') Я уже устал
  .result(v-if='needResult === true')
    table
      tr
        td
          a(v-on:click='sortBy="index"') Номер
        td
          a(v-on:click='sortBy="word"') Слово
        td
          a(v-on:click='sortBy="time"') Потрачено секунд
      tr(v-for='(game, index) in sortedList')
        td {{ index }}
        td {{ game.word }}
        td {{ game.time }}
</template>

<script>
export default {
  name: 'Playground',
  props: {
    playWord: Array,
    winWord: Array,
  },
  data() {
    return {
      sortBy: '',
      timeFromStart: 0,
      gamesList: [],
      hiddenLetters: [],
      enteredWord: [],
      isWin: false,
      needResult: false,
    };
  },
  computed: {
    sortedList() {
      // eslint-disable-next-line
      switch(this.sortBy) {
        // eslint-disable-next-line
        case 'index': return this.gamesList.sort((d1, d2) => (d1.index > d2.index) ? 1 : -1);
        // eslint-disable-next-line
        case 'word': return this.gamesList.sort((d1, d2) => (d1.word.toLowerCase() > d2.word.toLowerCase()) ? 1 : -1);
        // eslint-disable-next-line
        case 'time': return this.gamesList.sort((d1, d2) => (d1.time > d2.time) ? 1 : -1);
        default: return this.gamesList;
      }
    },
  },
  methods: {
    checkWin() {
      let entered = this.enteredWord;
      let correct = this.winWord;
      const timeFromEnd = Math.floor(new Date().valueOf() / 1000);
      const timeForGame = timeFromEnd - this.timeFromStart;
      entered = entered.join('');
      correct = correct.join('');
      if (entered === correct) {
        this.isWin = true;
        this.gamesList.push({
          word: correct,
          time: timeForGame,
        });
        console.log(this.gamesList);
      }
    },
    newGame() {
      this.enteredWord = [];
      this.hiddenLetters = [];
      this.timeFromStart = Math.floor(new Date().valueOf() / 1000);
    },
  },
  mounted() {
    this.newGame();
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="sass">
h3
  margin: 40px 0 0
ul
  list-style-type: none
  padding: 0
  display: flex
  justify-content: center
  align-content: center
  li
    display: inline-block
    margin: 5px 5px
    border: 1px solid gray
    width: 20px
    height: 20px
    cursor: pointer
ul.enterField
  background-color: #ddd
  height: 30px
  li
    cursor: default
a
  color: #42b983
  cursor: pointer
.result
  display: flex
  justify-content: center
  table
    border-left: 1px solid gray
    border-top: 1px solid gray
    border-spacing: 0
    tr
      td
        border-right: 1px solid gray
        border-bottom: 1px solid gray
        padding: 10px
</style>
