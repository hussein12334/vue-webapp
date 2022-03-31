<template>
  <div id="app">
    <div>
      <header>
        <h1> CLASS BOOKING SYSTEM </h1>
      </header>
      <div id="sort_panel" v-show="show_products || !this.len">
        <Strong>SORTING OPTIONS</Strong>
        <select v-model="sort.type">
          <option value="">Default</option>
          <option value="P">Price</option>
          <option value="A">Availability </option>
        </select>
        <br><br><br>
        <Strong>ORDER BY</Strong>
        <select v-model="sort.order_by">
          <option value="">Default</option>
          <option value="HL">High to Low</option>
          <option value="LH">Low to High</option>
        </select>
        <br><br><br>
        <input type="button" value="SORT" @click="sort_array">
      </div>
      <div id="search_bar" v-show="hideit">
        <strong>Search</strong>
        <input type="text" v-model= "search_term"><br>
      </div>
      <!-- button for the shopping cart -->
      <button v-show="len" @click="show_cart">{{this.cart.length}}  Shopping Cart</button>
      <br>
    </div>
    <div>
      <displayProducts @addproduct='add_to_cart' :lessons="lessons" v-show= show_products></displayProducts>
    </div>
    <checkout @removefromcart='remove_from_cart' @placeOrder='place_my_order' :cart="cart"  v-show= !show_products></checkout>
  </div>
</template>

<script>

import checkout from './components/cartList.vue'
import displayProducts from './components/lessonList.vue'


export default {
  name: 'app',
  components: {
    displayProducts, checkout
  },
  data()
  {
    return{
      search_term :'',
      lessons:[],
      show_products:true,
      ref:true,
      cart:[],
      len:false,
      sort:
      {
        type: '',
        order_by: ''
      }
    }
  },
//function to load data from MongoDB Atlas using GET request
  created: function () {
  fetch('https://webappcw.herokuapp.com/collection/Lessons')
    .then(response => response.json())
    .then(data => this.lessons = data)
    .catch(err=> console.log(err.message))
  },
  watch:{
    search_term() 
    {
      fetch('https://webappcw.herokuapp.com/collection/Lessons/'+ this.search_term)
      .then(response => response.json())
      .then(data => 
      this.lessons = data)
      .catch(err => console.log(err.message))
    }
  },

  computed:
  {
    filtered_lessons() {
      return this.lessons.filter((course) => {
      return course.subject.match(this.search_term) || course.campus.match(this.search_term);
      })
    },
    search_only() {
      return true;
    },
  },  
  methods: {
    place_my_order(order) 
    {
      //POST request to server
      fetch('https://webappcw.herokuapp.com/collection/Orders/',
      {
        method:'POST',
        headers:
        {
          'Content-Type' : 'application/json'
        },
        body:JSON.stringify(order)
      })
      .then(response =>
      {
        console.log(response);
        alert("Order Placed Successfully");
      })
      this.cart=[];
      this.len = false;
      this.show_product = true;
      this.hideit = true;
    },    
    show_cart() {
      if (this.show_products === true){
        this.show_products = false;
        this.hideit = false;
        }
        else {
          this.show_products = true;
          this.hideit = true;
        }
    },
    sort_array() {
      if (this.sort.type == "P" && this.sort.order_by == "HL") 
      {
        fetch('https://webappcw.herokuapp.com/collection/Lessons/price/-1')
        .then(response => response.json())
        .then(data => 
        this.lessons = data)
        .catch(err => console.log(err.message))
      }
      else if (this.sort.type == "P" && this.sort.order_by == "LH") {
        fetch('https://webappcw.herokuapp.com/collection/Lessons/price/1')
        .then(response => response.json())
        .then(data => 
        this.lessons = data)
        .catch(err => console.log(err.message))
      }
      else if (this.sort.type == "A" && this.sort.order_by == "HL") {
        fetch('https://webappcw.herokuapp.com/collection/Lessons/price/-1')
        .then(response => response.json())
        .then(data => 
        this.lessons = data)
        .catch(err => console.log(err.message))
      }          
      else if (this.sort.type == "A" && this.sort.order_by == "LH") {
        fetch('https://webappcw.herokuapp.com/collection/Lessons/1')
        .then(response => response.json())
        .then(data => 
        this.lessons = data)
        .catch(err => console.log(err.message))
      }           
      this.show_product=false;
      this.show_product=true;          
    },
    remove_from_cart(id_p) {
    //fix the broken code
    var deletedItem;
    for(var i=0;i<this.cart.length;i++)
    {
      if(this.cart[i]._id === id_p)
      {
        deletedItem = this.cart.splice(i,1);
      }          
    }             
    //checking the number of items in the cart
    if (this.cart.length === 0) 
    {
      this.len = false;
      this.show_products = true;
      this.hideit = true;
    }
    //needs to replace back to products
    var tempItem;
    for(i=0; i<this.lessons.length;i++)
    {
      if(this.lessons[i]._id === deletedItem[0]._id)
      {
        this.lessons[i].capacity = this.lessons[i].capacity + 1;
        this.lessons[i].A_D = false;
        tempItem = this.lessons[i];
      }
    }
    const tempObj = {capacity:tempItem.capacity ,A_D:tempItem.A_D};
    fetch('https://webappcw.herokuapp.com/collection/Lessons'+tempItem._id , 
    {
      method:'PUT',
      headers:{
        'Content-Type':'application/json'
      },
      body:JSON.stringify(tempObj)
    })
    .then(response =>
    {
      console.log(response);
    })        
  },
    //function to add items to the cart
    add_to_cart(id_p) 
    {
      for (var i = 0; i < this.lessons.length; i++)
      {
        if (this.lessons[i]._id === id_p) 
        {
          //pushing items to the cart array   
          this.cart.push(this.lessons[i]);
          //enabling the shopping cart button(as atleast one course is added at this point)
          this.len = true;
          if (this.lessons[i].capacity > 0) 
          {
            //needs to replace with PUT request 
            this.lessons[i].capacity = this.lessons[i].capacity - 1;
            if(this.lessons[i].capacity === 0)
            {
              this.lessons[i].A_D = true;
            }           
            const tempObj = {capacity:this.lessons[i].capacity ,A_D:this.lessons[i].A_D};
            fetch('https://webappcw.herokuapp.com/collection/Lessons/'+this.lessons[i]._id , 
            {
              method:'PUT',
              headers:{
                'Content-Type':'application/json'
              },
              body:JSON.stringify(tempObj)
            })
            .then(response =>
            {
              console.log(response);
            })
          }
        }
      }
    }    
  }
}
</script>

<style>
#app{
    background-color: rgba(107, 105, 105, 0.61);
    border-radius: 3%;
    font-family: verdana;
    width: 30%;
}

#app{
    
    padding: 15px;


}
#app img{
  object-fit: scale-down;
  height: 150px;
}

#app h1{
    color: red;
    margin-left:20px ;
}

#app button{
    margin: 10px;
    margin-left: 20px;
    padding: 10px;
    width: 80%;
}
</style>