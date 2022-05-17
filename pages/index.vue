<template>
  <div class="content row">
    <div class="col-xl-12 my-3">
        <b-alert
        :show="dismissCountDown"
        dismissible
        variant="warning"
        @dismissed="dismissCountDown=0"
        @dismiss-count-down="countDownChanged"
        >
        <p>{{ message }}</p>
        </b-alert>
    </div>
    <div class="col-xl-6">
      <h1>Daftar Pengguna</h1>
    </div>
    <div class="col-xl-6" align="end">
      <button type="button" class="btn btn-primary" @click="addUser()">Buat Pengguna</button>
    </div>
    <div class="col-xl-12">
      <b-card class="text-center">
        <table class="table">
          <thead>
            <tr>
              <th scope="col">No</th>
              <th scope="col">Name</th>
              <th scope="col">Email</th>
              <th scope="col">Gender</th>
              <th scope="col">Status</th>
              <th scope="col">Actions</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in items" v-bind:key="item.id">
              <td>{{index + 1}}</td>
              <td>{{item.name}}</td>
              <td>{{item.email}}</td>
              <td>{{item.gender}}</td>
              <td>{{item.status}}</td>
              <td>
                <button type="button" class="btn btn-info my-1" @click="viewDetail(item.id)">View</button>
                <button type="button" class="btn btn-warning my-1" @click="updateUser(item.id)">Update</button>
                <button type="button" class="btn btn-danger my-1" @click="deleteData(item.id)">Delete</button>
              </td>
            </tr>
          </tbody>
        </table>
        <nav aria-label="Page navigation example">
          <ul class="pagination">
            <li @click="prevPage()" class="page-item"><a class="page-link" href="#">Previous</a></li>
            <li v-for="(page, index) in items" v-bind:key="index" :class="select == index + 1 ? 'active' : ''" class="page-item" @click="nextPage(index)" ><a class="page-link" href="#">{{index + 1}}</a></li>
            <li @click="nextPage(index)" class="page-item"><a class="page-link" href="#">Next</a></li>
          </ul>
        </nav>
      </b-card>
    </div>
  </div>
</template>

<script lang="ts">
import vue from 'vue'
import axios from 'axios'

export default vue.extend({
  name: 'IndexPage',
    data() {
      return {
        items: [
          {id: 1, name: 'Mark', email: 'Mark@gmail.com', gender: 'Male', status: 'Single' },
          {id: 2, name: 'Jacob', email: 'Jacob@gmail.com', gender: 'Male', status: 'Single' },
          {id: 3, name: 'lary', email: 'Lary@gmail.com', gender: 'Male', status: 'Single' },
        ],
        message:'',
        dismissSecs: 3,
        dismissCountDown: 0,
        showDismissibleAlert: false,
        select: 0,
      }
    },
    mounted(){
      this.getData()
    },
    methods:{
      async prevPage(page){
        const data = axios.get('https://gorest.co.in/public/v2/users?page=' + page)
        this.items = await (await data).data
      },
      async nextPage(page){
        const data = axios.get('https://gorest.co.in/public/v2/users?page=' + page)
        this.items = await (await data).data
      },
      async getData(){
        const data = axios.get('https://gorest.co.in/public/v2/users')
        this.items = await (await data).data
      },
      async deleteData(id){
        const token = '9690fb8196780608aa119e9cadbf3901b8d6679995f2667a259a1f07fb7617cd'
        axios.delete(`https://gorest.co.in/public/v2/users/` + id, {
            headers: {
                'Authorization': `Bearer ${token}`
            }
        })
        .then(response => {
            this.message = 'Data Berhasil Dihapus'
            this.showAlert()
            this.getData()
        }).catch(response => {
            this.message = response.data.message
            this.showAlert()
        })
      },
      async viewDetail(id){
        this.$router.push({ name: 'detailUser', query: { id: id } })
      },
      async updateUser(id){
        this.$router.push({ name: 'updateUser', query: { id: id } })
      },
      async addUser(){
        this.$router.push({ name: 'addUser' })
      },
      countDownChanged(dismissCountDown) {
          this.dismissCountDown = dismissCountDown
      },
      showAlert() {
          this.dismissCountDown = this.dismissSecs
      }
    }
  })
</script>

<style lang="scss">
  .content{
    margin: 5%;
  }
  .is-left{
    text-align: end !important;
  }
</style>
