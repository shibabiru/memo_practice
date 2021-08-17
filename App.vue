<template>
  <div id="app">
    <h1 style="color: green">Phrase of the Day</h1>
    <div>
      <input v-model="newPhraseName">
      <button @click="addPhrase()" style="color: green">Add</button>
    </div>
  
    <ul style="list-style: none;">
      <li v-for="(phrase, key) in englishMemo" :key="key">
        <input type="checkbox" v-model="phrase.isComplete" @click="updatePhrase(phrase.key)">
        {{ phrase.name }}
        <button @click="removePhrase(key)" style="color: red">×</button>
      </li>
    </ul>
  </div>
</template>

<script>
import firebase from 'firebase';
export default {
  name: 'app',
  data(){
    return {
      db:null,
      englishMemoRef: null,
      newPhraseName: '',
      englishMemo: {}
    };
  },
  created(){
    this.db = firebase.firestore();
    this.englishMemoRef = this.db.collection('englishMemo');
    this.englishMemoRef.onSnapshot(querySnapshot => {
      const obj = {}
      querySnapshot.forEach(doc => {
        obj[doc.id] = doc.data()
      })
      this.englishMemo = obj
    })
  },
  methods: {
    addPhrase(){
      if(this.newPhraseName === '') { return }
      this.englishMemoRef.add({
        name: this.newPhraseName,
        isComplete: false,
      });
    },
    updatePhrase(phrase, key){
      phrase.isComplete = !phrase.isComplete;
      this.englishMemoRef.doc(key).update(phrase);
    },
    removePhrase(key){
      this.englishMemoRef.doc(key).delete();
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
