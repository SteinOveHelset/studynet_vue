<template>
<div>
  <Nav/>

  <router-view/>

  <Footer/>
</div>
</template>

<script>
import Footer from '@/components/Footer'
import Nav from '@/components/Nav'

export default {
  name: 'App',
  components: {
    Nav,
    Footer
  }
}
</script>

<style lang="scss">
@import '../node_modules/bulma';
</style>
