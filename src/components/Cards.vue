<template>
  <button v-on:click="toggleEditMode">{{ editMode ? 'Done' : 'Edit' }}</button>
  <button v-on:click="resetData" v-show="editMode">Reset</button>
  <ul>
    <li v-for="(card, i) in cardList" :key="`ct-${i}`">
      <a href="#" v-show="editMode" v-on:click="remove(card)">[x]</a>{{ card.term }}
      <ul>
        <li v-for="(sentence, i) in card.sentences" :key="`cs-${i}`">
          <a
            href="#"
            v-show="editMode"
            v-on:click="card.sentences = clear(card.sentences, sentence)"
            >[x]</a
          >
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
  <ul>
    <li v-for="(sentence, i) in sentenceList" :key="`sl-${i}`">
      <a href="#" v-show="editMode" v-on:click="sentenceList = clear(sentenceList, sentence)">
        [x]</a
      >{{ sentence }}
    </li>
  </ul>
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
      sentenceList: [],
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
            if (existingSentence.main == input) isUnique = false
            console.log(existingSentence.main, isUnique)
          })
          if (isUnique) {
            card.sentences.push({
              main: input,
              loc: [searchTerm, searchTerm + card.term.length],
            })
            let isSentenceUnique = true
            this.sentenceList.forEach((sentence) => {
              if (sentence == input) isSentenceUnique = false
            })
            if (isSentenceUnique) {
              this.sentenceList.push(input)
            }
          }
        }
      })

      event.target.value = ''
      this.save()
    },
    formatSentence: function (s) {
      return {
        pre: s.main.substring(0, s.loc[0]),
        term: s.main.substring(s.loc[0], s.loc[1]),
        post: s.main.substring(s.loc[1]),
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
      let sentence_data = JSON.stringify(this.sentenceList)
      localStorage.setItem('card_saved', card_data)
      localStorage.setItem('sentence_saved', sentence_data)
    },
    load: function () {
      let card_data = localStorage.getItem('card_saved')
      let sentence_data = localStorage.getItem('sentence_saved')
      if (card_data) {
        this.cardList = JSON.parse(card_data)
      }
      if (sentence_data) {
        this.sentenceList = JSON.parse(sentence_data)
      }
    },
    toggleEditMode: function () {
      this.editMode = !this.editMode
      this.findMode = !this.findMode
    },
    resetData: function () {
      localStorage.removeItem('card_saved')
      localStorage.removeItem('sentence_saved')
      location.reload()
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
a {
  padding: 0.3em;
}
</style>
