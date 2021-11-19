<template>
  <button v-on:click="toggleEditMode">{{ editMode ? 'Done' : 'Edit' }}</button>
  <ul>
    <li v-for="(grammarListObj, index) in grammarList" :key="`grammarListObj-${index}`">
      <a href="#" v-show="editMode" v-on:click="remove(grammarListObj)">[x]</a>
      {{ Object.keys(grammarListObj)[0] }}
      <ul>
        <li
          v-for="(sentence, index) in Object.values(grammarListObj)[0]"
          :key="`sentence-${index}`">
          <div>
            <a href="#" v-show="editMode" v-on:click="clear(grammarListObj, sentence)"> [x] </a>
            {{ sentence[0] }} <span class="grammarPoint">{{ sentence[1] }}</span
            >{{ sentence[2] }}
          </div>
        </li>
      </ul>
    </li>
  </ul>

  <input v-if="findMode" placeholder="Search Grammar" v-on:keyup.enter="findGrammar" />
  <input v-if="editMode" placeholder="Add Grammar" v-on:keyup.enter="addGrammar" />
  <br />
  <br />

  <button v-on:click="toggleAllSentences">
    {{ showAll ? 'Hide All Sentences' : 'Show All Sentences' }}
  </button>
  <ul>
    <li v-for="(sentence, index) in allSentences.all" v-show="showAll" :key="`s-${index}`">
      <div>
        <a href="#" v-show="editMode" v-on:click="clear(allSentences, sentence)">[x]</a
        >{{ sentence }}
      </div>
    </li>
  </ul>
</template>

<script>
export default {
  name: 'Sentences',
  data() {
    return {
      editMode: false,
      findMode: true,
      showAll: true,
      grammarList: [{ 恐らく: [] }, { に際して: [] }],
      allSentences: { all: [] },
    }
  },
  methods: {
    findGrammar: function (event) {
      let sentence = event.target.value
      let grammarObject = this.searchGrammar(sentence)
      console.log(grammarObject)
      grammarObject.grammar.length < 1 ? 'Nothing found' : this.addSentence(grammarObject)
      event.target.value = ''
      this.save()
    },
    updateSentence: function (array, sentence) {
      let isUnique = true
      array.forEach((s) => {
        if (s === sentence) {
          isUnique = false
        }
      })
      if (isUnique) array.push(sentence)
    },
    addSentence: function (grammarObject) {
      grammarObject.grammar.forEach((g) => {
        this.grammarList.forEach((obj) => {
          if (Object.keys(obj)[0] == g) {
            this.updateSentence(Object.values(obj)[0], grammarObject.breakdown)
            this.updateSentence(this.allSentences.all, grammarObject.sentence)
          }
        })
      })
    },
    getGrammar: function (array) {
      let list = []
      array.forEach((obj) => list.push(Object.keys(obj)[0]))
      return list
    },
    addGrammar: function (event) {
      this.grammarList.push({
        [event.target.value]: [],
      })
      event.target.value = ''
      this.save()
    },
    searchGrammar: function (s) {
      let object = {
        grammar: [],
        sentence: '',
        breakdown: [],
      }
      this.getGrammar(this.grammarList).forEach((item) => {
        let result = []
        let part = []
        let grammar = item.split('～') // for grammar points written as "A~B"
        grammar.forEach((grammarPart) => {
          let found = s.search(grammarPart)
          if (found > -1) result.push(s.substring(found, found + grammarPart.length))
          part.push(s.substring(0, found))
          part.push(s.substring(found, found + grammarPart.length))
          part.push(s.substring(found + grammarPart.length))
        })
        if (result.length === grammar.length) {
          object.grammar.push(item)
          object.sentence = s
          object.breakdown = part
        }
      })
      return object
    },
    remove: function (f) {
      this.grammarList = this.grammarList.filter((i) => i != f)
      this.save()
    },
    clear: function (obj, v) {
      let array = Object.values(obj)[0]
      array.splice(
        array.findIndex((i) => i == v),
        1,
      )
    },
    save: function () {
      let data = JSON.stringify(this.grammarList)
      let datatwo = JSON.stringify(this.allSentences)
      localStorage.setItem('saved', data)
      localStorage.setItem('savedtwo', datatwo)
    },
    load: function () {
      let data = localStorage.getItem('saved')
      let datatwo = localStorage.getItem('savedtwo')
      if (data) {
        this.grammarList = JSON.parse(data)
      }
      if (datatwo) {
        this.allSentences = JSON.parse(datatwo)
      }
    },
    toggleEditMode: function () {
      this.editMode = !this.editMode
      this.findMode = !this.findMode
      this.save()
    },
    toggleAllSentences: function () {
      this.showAll = !this.showAll
    },
  },
  created: function () {
    this.load()
  },
}
</script>

<style scoped>
.grammarPoint {
  color: darksalmon;
}
</style>
