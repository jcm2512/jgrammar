<template>
  <button v-on:click="toggleEditMode">{{ editMode ? 'Done' : 'Edit' }}</button>
  <ul>
    <li v-for="(card, i) in cardList" :key="`c-${i}`">
      <a href="#" v-show="editMode" v-on:click="remove(card)">[x]</a>
      {{ card.term }}
      <ul>
        <li v-for="(sentence, i) in card.sentence" :key="`s-${i}`">
          <a href="#" v-show="editMode" v-on:click="card.sentence = clear(card.sentence, sentence)">
            [x]
          </a>
          <span>
            {{ formatSentence(card.term, card.sentence[i]).pre }}
          </span>
          <span class="highlight">
            {{ formatSentence(card.term, card.sentence[i]).term }}
          </span>
          <span>
            {{ formatSentence(card.term, card.sentence[i]).post }}
          </span>
        </li>
      </ul>
    </li>
  </ul>
  <br /><br />
  <input v-if="editMode" placeholder="Add Card" v-on:keyup.enter="addCard" />
  <input v-if="findMode" placeholder="Add Sentence" v-on:keyup.enter="addSentence" />
  <br />
  <br />
</template>

<script>
export default {
  name: 'Cards',
  data() {
    return {
      editMode: false,
      findMode: true,
      showAll: true,
      cardList: [
        { term: 'hello', meaning: '', sentence: [] },
        { term: 'world', meaning: '', sentence: [] },
      ],
    }
  },
  methods: {
    addCard: function (event) {
      let card = { term: event.target.value, meaning: '', sentence: [] }
      this.cardList.push(card)
      event.target.value = ''
    },
    addSentence: function (event) {
      let input = event.target.value
      this.cardList.forEach((card) => {
        if (input.search(card.term) > -1) {
          let isUnique = true
          card.sentence.forEach((existingSentence) => {
            if (existingSentence == input) isUnique = false
          })
          if (isUnique) {
            card.sentence.push(input)
          }
        }
        event.target.value = ''
      })
      this.save()
      console.log(this.formatSentence('this', 'what is this here?'))
    },
    formatSentence: function (term, s) {
      let f = s.search(term)
      if (f > -1) {
        return {
          pre: s.substring(0, f),
          term: s.substring(f, f + term.length),
          post: s.substring(f + term.length),
        }
      }
    },
    remove: function (f) {
      this.cardList = this.cardList.filter((i) => i != f)
      this.save()
    },
    clear: function (array, searchPattern) {
      let filteredResults = array.filter((item) => {
        return item != searchPattern
      })
      return filteredResults
    },
    save: function () {
      let card_data = JSON.stringify(this.cardList)
      //let card_datatwo = JSON.stringify(this.allSentences);
      localStorage.setItem('card_saved', card_data)
      //localStorage.setItem("card_savedtwo", card_datatwo);
    },
    load: function () {
      let card_data = localStorage.getItem('card_saved')
      //    let card_datatwo = localStorage.getItem("card_savedtwo");
      if (card_data) {
        this.cardList = JSON.parse(card_data)
      }
      //    if (card_datatwo) {
      //      this.allSentences = JSON.parse(card_datatwo);
      //    }
    },
    toggleEditMode: function () {
      this.editMode = !this.editMode
      this.findMode = !this.findMode
      this.save()
    },
    //  toggleAllSentences: function () {
    //    this.showAll = !this.showAll;
    //  },
  },
  created: function () {
    this.load()
  },
}
</script>

<style scoped>
.highlight {
  color: darksalmon;
}
</style>
