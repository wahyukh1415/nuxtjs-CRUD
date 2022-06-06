<template>
    <div class="content row">
        <div class="col-xl-12">
            <h1>Lihat Pengguna</h1>
        </div>
        <div class="col-xl-12">
            <b-card>
                <table style="width:100%">
                    <tr>
                        <td class="my-3">Name</td>
                        <td>: {{name}}</td>
                    </tr>
                    <tr>
                        <td class="my-3">Email</td>
                        <td>: {{email}}</td>
                    </tr>
                    <tr>
                        <td class="my-3">Gender</td>
                        <td>: {{gender}}</td>
                    </tr>
                    <tr>
                        <td class="my-3">Status</td>
                        <td>: {{status}}</td>
                    </tr>
                </table>
                <div class="row my-3">
                    <div class="col">
                        <h5>Post Pengguna</h5>
                    </div>
                    <div class="col" align="end">
                        <b-button id="show-btn" @click="modal = !modal">Buat Post</b-button>
                    </div>
                </div>
                <div class="my-2">
                    <b-card>
                        <table v-if="listPost.length" class="table">
                            <thead>
                                <th>Title</th>
                                <th>Body</th>
                                <th>Actions</th>
                            </thead>
                            <tbody v-for="(list, index) in listPost" v-bind:key="index">
                                <tr>
                                    <td>{{list.title}}</td>
                                    <td>{{list.body}}</td>
                                    <td>
                                        <button type="button" class="btn btn-danger" @click="deletePost(list.id)">Delete</button>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                        <div class="row" v-if="!listPost.length">
                            <div class="col" style="text-align: center">Data Post Tidak Ada</div>
                        </div>
                    </b-card>
                </div>
            </b-card>
        </div>
        <div class="col-xl-12 my-3" align="end">
            <button type="button" class="btn btn-secondary" @click="back()">Kembali</button>
        </div>
        <b-modal v-model="modal" id="bv-modal-example" hide-footer size="lg">
            <template #modal-title>
                    Dialog Box - Buat Post
            </template>
            <div class="d-block">
            <div class="row">
                <div class="col">
                </div>
                <div class="col" align="end">
                    <b-button class="mt-3" @click="submit()">Simpan</b-button>
                </div>
            </div>
            <form>
            <div class="form-group">
              <label for="exampleFormControlInput1">Title</label>
              <input type="text" class="form-control" id="exampleFormControlInput1" placeholder="Title" v-model="title">
            </div>
            <div class="form-group">
              <label for="exampleFormControlTextarea1">Body</label>
              <textarea class="form-control" id="exampleFormControlTextarea1" rows="3" placeholder="Body" v-model="body"></textarea>
            </div>
            </form>
            </div>
        </b-modal>
    </div>
</template>

<script>
import Vue from 'vue'
import axios from 'axios'

export default Vue.extend({
    name: 'IndexPage',
    data() {
      return {
        name: "",
        email: "",
        gender: "",
        status: "",
        listPost: [],
        title: "",
        body:"",
        modal: false
      }
    },
    mounted(){
      this.getData(),
      this.getDataPost()
    },
    methods:{
      async getData(){
        const data = axios.get('https://gorest.co.in/public/v2/users/' + this.$route.query.id)
        this.name = await (await data).data.name
        this.email = await (await data).data.email
        this.gender = await (await data).data.gender
        this.status = await (await data).data.status
      },
      async getDataPost(){
        const data = axios.get('https://gorest.co.in/public/v2/users/' + this.$route.query.id + '/posts')
        this.listPost = await (await data).data
      },
      async back(){
        this.$router.push("/");
      },
      async submit(){
            const token = '9690fb8196780608aa119e9cadbf3901b8d6679995f2667a259a1f07fb7617cd'
            const body = {
                title: this.title,
                body: this.body
            }
            let self = this
            const data = axios.post('https://gorest.co.in/public/v2/users/'+ this.$route.query.id +'/posts', body,  {
                headers: {
                    'Authorization': `Bearer ${token}`
                }
            })
            .then((response) =>{
                if(response.status === 201){
                    this.modal = false
                    this.getData(),
                    this.getDataPost()
                }
            })
            .catch(({response}) => {
                console.log(response)
                self.message = response.data[0].field + ' ' + response.data[0].message
                self.showAlert()
            });
        },
        async deletePost(id) {
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
    }
})
</script>

<style lang="scss">
.content {
    margin: 5%;
}
</style>