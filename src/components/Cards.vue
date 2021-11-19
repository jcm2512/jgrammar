<template>
  <button v-on:click="toggleEditMode">{{ editMode ? "Done" : "Edit" }}</button>
  <ul>
    <li v-for="(card, index) in cardList" :key="`card-${index}`">
      {{ card.term }}
    </li>
  </ul>
  <br /><br />
  <input v-if="editMode" placeholder="Add Card" v-on:keyup.enter="addCard" />
  <input
    v-if="findMode"
    placeholder="Add Sentence"
    v-on:keyup.enter="addCard"
  />
  <br />
  <br />
</template>

<script>
export default {
  name: "Cards",
  data() {
    return {
      editMode: false,
      findMode: true,
      showAll: true,
      cardList: [
        { term: "hello", meaning: "", sentence: "" },
        { term: "world", meaning: "", sentence: "" },
      ],
    };
  },
  methods: {
    addCard: function (event) {
      let card = { term: event.target.value, meaning: "", sentence: "" };
      this.cardList.push(card);
      event.target.value = "";
    },

    //  findGrammar: function (event) {
    //    let sentence = event.target.value;
    //    let grammarObject = this.searchGrammar(sentence);
    //    console.log(grammarObject);
    //    grammarObject.grammar.length < 1
    //      ? "Nothing found"
    //      : this.addSentence(grammarObject);
    //    event.target.value = "";
    //    this.save();
    //  },
    //  updateSentence: function (array, sentence) {
    //    let isUnique = true;
    //    array.forEach((s) => {
    //      if (s === sentence) {
    //        isUnique = false;
    //      }
    //    });
    //    if (isUnique) array.push(sentence);
    //  },
    //  addSentence: function (grammarObject) {
    //    grammarObject.grammar.forEach((g) => {
    //      this.cardList.forEach((obj) => {
    //        if (Object.keys(obj)[0] == g) {
    //          this.updateSentence(Object.values(obj)[0], grammarObject.breakdown);
    //          this.updateSentence(this.allSentences.all, grammarObject.sentence);
    //        }
    //      });
    //    });
    //  },
    //  getGrammar: function (array) {
    //    let list = [];
    //    array.forEach((obj) => list.push(Object.keys(obj)[0]));
    //    return list;
    //  },
    //  addGrammar: function (event) {
    //    this.cardList.push({
    //      [event.target.value]: [],
    //    });
    //    event.target.value = "";
    //    this.save();
    //  },
    //  searchGrammar: function (s) {
    //    let object = {
    //      grammar: [],
    //      sentence: "",
    //      breakdown: [],
    //    };
    //    this.getGrammar(this.cardList).forEach((item) => {
    //      let result = [];
    //      let part = [];
    //      let grammar = item.split("～"); // for grammar points written as "A~B"
    //      grammar.forEach((grammarPart) => {
    //        let found = s.search(grammarPart);
    //        if (found > -1)
    //          result.push(s.substring(found, found + grammarPart.length));
    //        part.push(s.substring(0, found));
    //        part.push(s.substring(found, found + grammarPart.length));
    //        part.push(s.substring(found + grammarPart.length));
    //      });
    //      if (result.length === grammar.length) {
    //        object.grammar.push(item);
    //        object.sentence = s;
    //        object.breakdown = part;
    //      }
    //    });
    //    return object;
    //  },
    //  remove: function (f) {
    //    this.cardList = this.cardList.filter((i) => i != f);
    //    this.save();
    //  },
    //  clear: function (obj, v) {
    //    let array = Object.values(obj)[0];
    //    array.splice(
    //      array.findIndex((i) => i == v),
    //      1
    //    );
    //  },
    save: function () {
      let card_data = JSON.stringify(this.cardList);
      //let card_datatwo = JSON.stringify(this.allSentences);
      localStorage.setItem("card_saved", card_data);
      //localStorage.setItem("card_savedtwo", card_datatwo);
    },
    load: function () {
      let card_data = localStorage.getItem("card_saved");
      //    let card_datatwo = localStorage.getItem("card_savedtwo");
      if (card_data) {
        this.cardList = JSON.parse(card_data);
      }
      //    if (card_datatwo) {
      //      this.allSentences = JSON.parse(card_datatwo);
      //    }
    },
    toggleEditMode: function () {
      this.editMode = !this.editMode;
      this.findMode = !this.findMode;
      this.save();
    },
    //  toggleAllSentences: function () {
    //    this.showAll = !this.showAll;
    //  },
  },
  created: function () {
    this.load();
  },
};
</script>

<style scoped>
.grammarPoint {
  color: darksalmon;
}
</style>