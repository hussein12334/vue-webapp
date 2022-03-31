<template>
<div >
    <p1>First Name</p1>
    <input type="text" id="fname" v-model="order.first_name"><br><br>
    <p1>Last Name</p1>
    <input type="text" id="lname" v-model="order.last_name"><br><br>
    <p1>Phone Number</p1>
    <input type="text" id="phone" v-model="order.phone_number"><br><br>
    <p1>Post Code</p1>
    <input type="text" id="postcode" v-model="order.postcode"><br><br>
    <p1>Address Line</p1>
    <input type="text" id="address" v-model="order.address"><br><br>
    <input type="button" value="Place Order" v-show="all_info" @click="place_my_order">
    <!-- The checout page starts here -->
      <div v-for="c in cart" :key="c.id">
        <div>
            <img id="img" v-bind:src="c.image">
            <p>{{c.subject}}</p>
            <p>{{c.price}}</p>
            <input type="button" value="Remove" @click="remove_from_cart(c._id)">
        </div>
      </div>
    <!-- This div is to take input for placing order -->
    
</div>   
</template>

<script>
  var myregex1 = /^\d+\d*$/;
  var myregex2 = /^[a-z]+[a-z]*$/;
export default{
    name: "checkOut",
    props:['cart'],
    data(){
        return{
            s_p : !(this.show_products),
            order:
            {
                first_name:'',
                last_name: '',
                phone_number: '',
                postcode: '',
                address: '',
                items: []
            },
        }
    },
    computed:
    {
        all_info() {
            //checking if all the information is correct in the form
            if (myregex1.test(this.order.phone_number) && myregex2.test(this.order.first_name) && myregex2.test(this.order.last_name) ){
            return true;
        }
      else{
        return false;
      }
    }
    },
    methods:
    {
        remove_from_cart(id)
        {
            this.$emit('removefromcart',id);
        },
        place_my_order()
        {
            this.order.items = this.cart;
            this.$emit('placeOrder',this.order);
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