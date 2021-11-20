<template>
  <button v-on:click="toggleEditMode">{{ editMode ? 'Done' : 'Edit' }}</button>
  <ul>
    <li v-for="(card, i) in cardList" :key="`c-${i}`">
      <a href="#" v-show="editMode" v-on:click="remove(card)">[x]</a>
      {{ card.term }}
      <ul>
        <li v-for="(sentence, i) in card.sentences" :key="`s-${i}`">
          <a
            href="#"
            v-show="editMode"
            v-on:click="card.sentences = clear(card.sentences, sentence)">
            [x]
          </a>
          <span>
            {{ formatSentence(card.sentences[i]).pre }}
          </span>
          <span class="highlight">
            {{ formatSentence(card.sentences[i]).term }}
          </span>
          <span>
            {{ formatSentence(card.sentences[i]).post }}
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
        { term: 'hello', meaning: '', sentences: [] },
        { term: 'world', meaning: '', sentences: [] },
      ],
    }
  },
  methods: {
    addCard: function (event) {
      let card = { term: event.target.value, meaning: '', sentences: [] }
      this.cardList.push(card)
      event.target.value = ''
    },
    addSentence: function (event) {
      let input = event.target.value
      this.cardList.forEach((card) => {
        const searchTerm = input.search(card.term)
        if (searchTerm > -1) {
          let isUnique = true
          card.sentences.forEach((existingSentence) => {
            if (existingSentence == input) isUnique = false
          })
          if (isUnique) {
            card.sentences.push([input, [searchTerm, searchTerm + card.term.length]])
          }
        }
        event.target.value = ''
      })
      this.save()
    },
    formatSentence: function (s) {
      return {
        pre: s[0].substring(0, s[1][0]),
        term: s[0].substring(s[1][0], s[1][1]),
        post: s[0].substring(s[1][1]),
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
