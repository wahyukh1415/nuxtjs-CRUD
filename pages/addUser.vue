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
        <div class="col-xl-12">
            <h1>Tambah Pengguna</h1>
        </div>
        <div class="col-xl-12">
            <b-card>
                <form>
                  <div class="form-group">
                    <label for="exampleFormControlInput1">Nama</label>
                    <input type="text" class="form-control" id="exampleFormControlInput1"
                      placeholder="nama" v-model="name">
                  </div>
                  <div class="form-group">
                    <label for="exampleFormControlInput1">Email address</label>
                    <input type="email" class="form-control" id="exampleFormControlInput1"
                      placeholder="name@example.com" v-model="email">
                  </div>
                  <div class="form-group">
                    <label for="exampleFormControlInput1">Gender</label>
                    <select class="form-control" id="exampleFormControlSelect1" v-model="gender">
                        <option selected disabled>Pilih Gender</option>
                        <option>male</option>
                        <option>female</option>
                    </select>
                  </div>
                  <div class="form-group">
                    <label for="exampleFormControlInput1">Status</label>
                    <select class="form-control" id="exampleFormControlSelect1" v-model="status">
                        <option selected disabled>Pilih Status</option>
                        <option>active</option>
                        <option>inactive</option>
                    </select>
                  </div>
                </form>
                <div class="row">
                    <div class="col">
                        <button type="button" class="btn btn-secondary" @click="back()">Kembali</button>
                    </div>
                    <div class="col" align="end">
                        <button type="button" class="btn btn-success" @click="submit()">Simpan</button>
                    </div>
                </div>
            </b-card>
        </div>
    </div>
</template>

<script lang="ts">
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
        message: "",
        dismissSecs: 3,
        dismissCountDown: 0,
        showDismissibleAlert: false
      }
    },
    mounted(){
        this.getData()
    },
    methods:{
        async back(){
            this.$router.push("/");
        },
        async submit(){
            const token = '9690fb8196780608aa119e9cadbf3901b8d6679995f2667a259a1f07fb7617cd'
            const body = {
                name: this.email,
                email: this.email,
                gender: this.gender,
                status: this.status
            }
            let self = this
            const data = axios.post('https://gorest.co.in/public/v2/users/', body,  {
                headers: {
                    'Authorization': `Bearer ${token}`
                }
            })
            .then((response) =>{
                if(response.status === 201){
                    self.$router.push("/");
                }
            })
            .catch(({response}) => {
                console.log(response)
                self.message = response.data[0].field + ' ' + response.data[0].message
                self.showAlert()
            });
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
.content {
    margin: 5%;
}
</style>