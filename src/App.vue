<template>
  <div id="app" class="container">
    <img src="./assets/logo.png">
    <h1>Vue + Firebase = Bookmark</h1> 

    <div class="card text-white bg-dark mb-3">
      <div class="card-header">
        <h3>Adiciona o link</h3>
      </div>

      <div class="card-body">
        <form v-on:submit.prevent="addLink" class="form-inline">
          <label for="">Title</label>
          <div class="form-group">
            <input 
            v-model="newLink.title"
            type="text" 
            placeholder="Title"
            class="form-control">
          </div>

          <div class="form-group">
            <label for="">Site</label>
            <input 
            v-model="newLink.author"
            type="text" 
            placeholder="Author"
            class="form-control">
          </div>

          <div class="form-group">
            <label for="">URL</label>
            <input 
            v-model="newLink.url"
            type="text" 
            placeholder="Url"
            class="form-control">
          </div>

          <input type="submit" class="btn btn-success" value="Add a Link">
          
        </form>
      </div>
    </div>
  
      <hr>
      <div class="card text-white bg-dark mb-3">
        <div class="card-header">
          <h3 class="card-title">Links List</h3>
        </div>
        <div class="card-body">
          <table class="table table-striped">
            <thead>
              <th>Title</th>
              <th>Site</th>
              <th>Delete</th>
            </thead>
            <tbody>
              <tr v-for="link in links" :key="link.id">
                <td>
                  <a target="_blank" v-bind:href="link.url">{{ link.title }}</a>
                </td>
                <td>
                  {{ link.author }}
                </td>
                <td>
                  <button 
                  v-on:click="deleteLink(link)" 
                  class="btn btn-danger">
                    <i class="fa fa-trash-o" aria-hidden="true"></i>
                  </button>
                  </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
  </div>
</template>

<script>
import Firebase from 'firebase';
import toastr from 'toastr';

let config = {
  apiKey: "AIzaSyDIyiuSCs3SMUd2Ii5AsRAb-1dMqf49GtE",
    authDomain: "bookmarkwithvue.firebaseapp.com",
    databaseURL: "https://bookmarkwithvue.firebaseio.com",
    projectId: "bookmarkwithvue",
    storageBucket: "bookmarkwithvue.appspot.com",
    messagingSenderId: "1062250845467"
};

let app = Firebase.initializeApp(config);
let db = app.database();

let linksRef = db.ref('links');

export default {
  name: 'App',
  firebase: {
    links: linksRef
  },
  data() {
    return {
      newLink: {
        title: '',
        author: '',
        url: ''
      }
    }
  },
  methods: {
    addLink: function () {
      linksRef.push(this.newLink);
      this.newLink.title = '',
      this.newLink.author = '',
      this.newLink.url = ''
    },
    deleteLink: function (link) {
      linksRef.child(link['.key']).remove();
      toastr.success('Link Removed Successfully');
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #0b8b27;
  margin-top: 60px;
}
</style>
