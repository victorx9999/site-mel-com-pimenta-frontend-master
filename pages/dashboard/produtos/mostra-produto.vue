<template>
  <main class="container">
  <!-- <router-link to="/dashboard/produtos/listar-produtos"><i class="fa fa-arrow-left">Volta</i></router-link> -->
  <router-link to="/dashboard/produtos/listar-produtos"><i class="btn btn-primary">Volta</i></router-link> 
   
   <div class="left-column">
        <img :src="'http://localhost:8000/storage/image_product/' + posts.image_product" :alt="`${posts.image_product}`" > 
    </div>
    
 <div class="right-column">

    <div class="product-description">
       <h1>{{posts.name}}</h1>
       <p>{{posts.description}}</p>
       <p>Quantidade: {{posts.amount}}</p>
       <p>Desconto: {{posts.discount}}%</p>
       <p>Categoria: {{postsCat.category}}</p>
       <p>SubCategoria: {{postsSub.subcategory}}</p>
       <p>Sabores: {{posts.flavor}}</p>
  </div>

     <div class="product-price">
      <span>Preço: {{posts.gross_price}} R$</span>
     <!-- <a href="#" class="cart-btn">Adicionar ao carrinho</a> -->
    </div>
  </div>
</main>
  
</template>
 <script> 
 
export default {
  data() {
    return {
      posts: [],
      errors: [],
      postsSub:[],
      postsCat:[] 
    }
  }, 
   
      created () {
    let id = this.$route.query.id;
    let sb = this.$route.query.sb;
    let ct = this.$route.query.ct; 
    this.$axios.get(`/product/`+id)
      .then(response => {
       
        this.posts = response.data
        
        
      }),
      
      this.$axios.get(`/subcategory/`+sb)
      .then(response =>{
        this.postsSub = response.data
      }),
      this.$axios.get(`/category/`+ct)
      .then(response =>{
        this.postsCat = response.data
      })
  }


}
</script>

<style>
template, body {
  height: 100%;
  width: 100%;
  margin: 0;
  font-family: 'Roboto', sans-serif;
}
 
.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 15px;
  display: flex;
}

.left-column {
  width: 65%;
  position: relative;
}
 
.right-column {
  width: 35%;
  margin-top: 40px;
}


 .left-column img {
  width: 50%;
  position: absolute;
  left: 150px;
  top: 0;
  
  transition: all 0.3s ease;
}
 
.left-column img.active {
  opacity: 1;
}

.product-description {
  border-bottom: 1px solid #E1E8EE;
  margin-bottom: 20px;
}
.product-description span {
  font-size: 12px;
  color: #358ED7;
  letter-spacing: 1px;
  text-transform: uppercase;
  text-decoration: none;
}
.product-description h1 {
  font-weight: 300;
  font-size: 52px;
  color: #43484D;
  letter-spacing: -2px;
}
.product-description p {
  font-size: 16px;
  font-weight: 300;
  color: #86939E;
  line-height: 24px;
}

.product-price {
  display: flex;
  align-items: center;
}
 
.product-price span {
  font-size: 26px;
  font-weight: 300;
  color: #43474D;
  margin-right: 20px;
}
 
.cart-btn {
  display: inline-block;
  background-color: #7DC855;
  border-radius: 6px;
  font-size: 16px;
  color: #FFFFFF;
  text-decoration: none;
  padding: 12px 30px;
  transition: all .5s;
}
.cart-btn:hover {
  background-color: #64af3d;
}


</style>