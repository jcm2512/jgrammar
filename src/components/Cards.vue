<template>
  <button v-on:click="toggleEditMode">{{ editMode ? "Done" : "Edit" }}</button>
  <ul>
    <li v-for="(card, i) in cardList" :key="`c-${i}`">
      {{ card.term }}
      <ul>
        <li v-for="(sentence, i) in card.sentence" :key="`s-${i}`">
          {{ card.sentence[i] }}
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
  name: "Cards",
  data() {
    return {
      editMode: false,
      findMode: true,
      showAll: true,
      cardList: [
        { term: "hello", meaning: "", sentence: [] },
        { term: "world", meaning: "", sentence: [] },
      ],
    };
  },
  methods: {
    addCard: function (event) {
      let card = { term: event.target.value, meaning: "", sentence: [] };
      this.cardList.push(card);
      event.target.value = "";
    },
    addSentence: function (event) {
      let input = event.target.value
      this.cardList.forEach((card) => {
        if (input.search(card.term) > -1) { // look for matching term
          let isUnique = true
          // check if sentence already exists
          card.sentence.forEach((existingSentence) => {
            if (existingSentence == input) isUnique = false
          });
          // append sentence
          if (isUnique) {
            card.sentence.push(input)
          }
        } else {
          this.nothingFound()
        }
        event.target.value = ""
      })
      this.save();
    },
    nothingFound: function () {
      console.log("Nothing Found")
    },
    

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