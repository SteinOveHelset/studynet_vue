<template>
  <div class="about">
    <div class="hero is-info">
      <div class="hero-body has-text-centered">
        <h1 class="title">My Account</h1>
      </div>
    </div>

    <section class="section">
      <button @click="logout()" class="button is-danger">Log out</button>
    </section>
  </div>
</template>

<script>
import axios from 'axios'

export default {
    mounted() {
        axios
            .get('activites/get_active_courses/')
            .then(response => {
                console.log(response.data)
            })
    },
    methods: {
        async logout() {
            console.log('logout')

            await axios
              .post('token/logout/')
              .then(response => {
                console.log('Logged out')
              })
              .catch(error => {
                console.log(error)
              })

            axios.defaults.headers.common['Authorization'] = ""

            localStorage.removeItem('token')

            this.$store.commit('removeToken')

            this.$router.push('/')
        }
    }
}
</script>