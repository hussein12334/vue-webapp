<template>
    <div>
      <div v-for="lesson in lessons"  :key="lesson.id">
        <div>
         <img id="img" v-bind:src="lesson.image">
          <h2>Subject : {{lesson.subject}}</h2>
          <p>Location : {{lesson.location}}</p>
          <p>Price : {{lesson.price}}</p>
          <p>Available Spaces : {{lesson.capacity}}</p>
          <input id="button" type="button" value="Add To Cart" :disabled="lesson.A_D" @click="addtocart(lesson._id)">
        </div>
      </div>
    </div>
</template>

<script>
export default{
    name: "displayProducts",
    props:['lessons'],
    data(){
        return{ lessonsL : this.lessons}
    },
    computed :{
        filtered_lessons() {
            return this.lessons.filter((lesson) => {
            return lesson.subject.match(this.search_term) || lesson.campus.match(this.search_term);
            })
        }
    },
    methods: {
        addtocart(id){
            this.$emit('addproduct',id);
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