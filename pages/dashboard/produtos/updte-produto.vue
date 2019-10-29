<template>
  <div >
      <h1>UPDATE</h1>
   
       <router-link to="/dashboard/produtos/listar-produtos"><i class="fa fa-arrow-left">Volta</i></router-link>
    
    
      <h2>{{posts.name}}</h2>
      <hr>
      <form v-on:submit.prevent="submitPostEdit()">
          <div class="form-group">
            <span>{{posts.name}}</span>
            <input type="text" v-model="postBody.name" placeholder="nome" class="form-control">
          </div>
          <div class="form-group">
            <button type="submit" class="btn btn-success">Atualizar</button>
          </div>

        </form>
     
    
  </div>
</template>


 <script> 
 
export default {
  data() {
    return {
      posts: [],
      postBody: {
        name: ''
      },
      errors: []
    }
  }, 
   created () {
       let id = this.$route.query.id
    //   this.$axios.get(`/product/` +id + '/edit')
    //   .then(response => {
    //     this.posts = response.data
    //   })
    //   .catch(e => {
    //     this.errors.push(e)
    //   })
     
    this.$axios.get(`/product/`+id)
      .then(response => {
        this.posts = response.data
      })
      .catch(e => {
        this.errors.push(e)
      })
  },
  methods: {
    submitPostEdit () {
      let id = this.$route.query.id
      this.$axios.patch(`/product/`+id,this.postBody)
        .then(response => {
          console.log(response)
          
          this.postBody = response.data
        })
        .catch(e => {
          this.errors.push(e)
        })
    }
  }
}
</script>