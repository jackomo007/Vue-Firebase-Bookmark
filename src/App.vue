<template>
  <div id="app" class="container bg-dark">
    <img src="./assets/logo.png">
    <h1 style="font-size: 4vw;">Vue + Firebase = Vuemark</h1>
    <radial-menu
    class="text-white"
    style="margin: auto; background-color: #35495E"
    :itemSize="50"
    :radius="100"
    :rotate="0"
    :angle-restriction="180"
    >
      <radial-menu-item
        v-for="(item, index) in actionItems"
        :key="index"
        v-bind:style=" item === 'add' ? 'background-color: #28A745;' : 'background-color: #007BFF;' "
        style="background-color: white"
        @click="() => actionsClick(item)"
      >
        <span v-if="item === 'add'"><i class="fa fa-plus-circle" aria-hidden="true"></i></span>
        <span v-if="item === 'list'"><i class="fa fa-list" aria-hidden="true"></i></span>
      </radial-menu-item>
    </radial-menu>
    <div class="card text-white bg-dark mb-3">
      <div class="card-body" style="display: grid;grid-template-columns: repeat(10, auto);grid-gap: 2vw;justify-content: center;">
         <div v-for="link in links" :key="link.id">
          <a target="_blank" v-bind:href="'https://'+link.url">
            <i
            style="font-size: 5vw;"
            v-bind:class="link.icon"
            aria-hidden="true"
            v-bind:style="'color:'+link.color"
            >
            </i>
          </a>
        </div>
      </div>
    </div>
    <div v-if="addLinks" class="card text-white bg-dark mb-3">
      <div class="card-header">
        <h3>Adiciona o link</h3>
      </div>

      <div class="card-body">
        <form v-on:submit.prevent="addLink" class="grid-form">
          <label for="">Title</label>
          <div class="form-group">
            <input
            v-model="newLink.title"
            type="text"
            placeholder="Title"
            class="form-control">
          </div>

          <div class="form-group">
            <label for="">Icon <a target="_blank" href="https://fontawesome.com/v4.7.0/icons/"><i class="fa fa-search" aria-hidden="true"></i></a></label>
            <input
            v-model="newLink.icon"
            type="text"
            placeholder="fa-facebook-square"
            class="form-control">
          </div>

          <div class="form-group">
            <label for="">Color</label>
            <select
              v-model="newLink.color"
              class="form-control">
              <option value="#2962ff" style="color:#2962ff">blue</option>
              <option value="#ffeb3b" style="color:#ffeb3b">yellow</option>
              <option value="white" style="color:#cecece">white</option>
              <option value="#e91e63" style="color:#e91e63">red</option>
              <option value="#388e3c" style="color:#388e3c">green</option>
              <option value="#212121" style="color:#212121">black</option>
              <option value="#78909c" style="color:#78909c">grey</option>
            </select>
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
    <div v-if="listLinks" class="card text-white bg-dark mb-3">
      <div class="card-header">
        <h3 class="card-title">Links List</h3>
      </div>
      <div class="card-body">
        <table class="table table-striped">
          <thead>
            <th>Title</th>
            <th>Icon</th>
            <th>Actions</th>
          </thead>
          <tbody>
            <tr v-for="link in links" :key="link.id">
              <td style="font-variant: all-petite-caps;">
                {{ link.title }}
              </td>
              <td>
                <i
                v-bind:class="link.icon"
                aria-hidden="true"
                v-bind:style="'color:'+link.color"></i>
              </td>
              <td>
                  <radial-menu
                  style="margin: auto; background-color: #35495E"
                  :itemSize="50"
                  :radius="100"
                  :rotate="0"
                  :angle-restriction="180"
                  >
                    <radial-menu-item
                      v-for="(item, index) in items"
                      :key="index"
                      v-bind:style=" item === 'delete' ? 'background-color: #DC3545;' : 'background-color: white;' "
                      style="background-color: black"
                      @click="() => handleClick(item, link)"
                    >
                      <span v-if="item === 'delete'"><i class="fa fa-trash-o" aria-hidden="true"></i></span>
                      <span v-if="item === 'view'"><a target="_blank" v-bind:href="'https://'+link.url"><i class="fa fa-eye" aria-hidden="true"></i></a></span>
                    </radial-menu-item>
                  </radial-menu>
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
  import { RadialMenu,  RadialMenuItem } from 'vue-radial-menu'

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
    components: {
      RadialMenu,
      RadialMenuItem
    },
    data() {
      return {
        newLink: {
          title: '',
          icon: '',
          color: '',
          url: ''
        },
        items: ['view', 'delete'],
        actionItems: ['add', 'list'],
        listLinks: false,
        addLinks: false
      }
    },
    methods: {
      addLink: function () {
        this.newLink.icon = 'fa '+this.newLink.icon;
        linksRef.push(this.newLink);
        toastr.success('Link Added Successfully');
        this.newLink.title = '',
        this.newLink.icon = '',
        this.newLink.color = '',
        this.newLink.url = ''
      },
      deleteLink: function (link) {
        linksRef.child(link['.key']).remove();
        toastr.error('Link Removed Successfully');
      },
      handleClick (item, link) {
        if(item === 'delete'){
          this.deleteLink(link);
        }
      },
      actionsClick (item) {
        if(item === 'list'){
          this.listLinks = !this.listLinks ? true : false;
        }else{
          this.addLinks = !this.addLinks ? true : false;
        }
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
  .grid-form{
    display: grid;
  }
</style>
