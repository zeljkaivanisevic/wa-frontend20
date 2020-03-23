<template>
  <div v-if="store.authenticated">
    <button @click="newImage()" type="Novi post" class="btn btn-primary ml-2">Post new image</button>
    <div @click="gotoDetails(post)" :key="post.id" v-for="post in posts">
      <InstagramCard :info="post"/>
    </div>
  </div>
</template>

<script>
import _ from 'lodash'
import InstagramCard from "@/components/InstagramCard.vue";
import store from "@/store.js";

export default {
  data() {
    return {
      store,
      posts: [],
      term: ""

    }
  },

watch: {
 "store.search": _.debounce(function(val) {this.fetchPosts(val)}, 500),
  
 //"store.searchTerm": function(val) {this.fetchPosts(val)}
 },

name: "posts",

   mounted() {
 this.fetchPosts();
   },

  
  methods: {


fetchPosts() {

 fetch(`http://localhost:3000/posts207?_any=${this.store.search}`)
 .then(response => {
 return response.json()
 })
 .then(data => {
 console.log("Podaci s backenda", data)
 this.posts = data.map(doc => {
 return {id: doc.id, url: doc.source, email: doc.createdBy, title:
doc.title, posted_at: Number(doc.postedAt)}
 })
 })
 },
   

    gotoDetails(card) {
      this.$router.push({path: `post/${card.id}`})
    },
    newImage() {
      this.$router.push({name: 'newpost'}).catch(err => console.log(err))
    }
  },
  components: {
    InstagramCard
  }
}
</script>

<style scoped>
  button {
    margin-bottom: 20px
  }
</style>