<template>
  <div>
    <h1>{{ "PETS" }}</h1>
    <div  v-for="item,index in catListByGender" :key="index">
      <h3>{{ item.gender }}</h3>
      <ul>
        <li v-for="cat in item.catList" :key="cat.index" >{{ cat.name }}</li>
      </ul>
    </div>
    <div v-if="loading" class="loading"> 加载中... </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: 'PetList',
  data(){
    return {
      catListByGender:[],
      loading: true
    }
  },
  methods: {
    async renderList(){
      this.loading = true
      try{
        const res = await axios({
        method: "get",
        url: "http://5c92dbfae7b1a00014078e61.mockapi.io/owners",
        })
        this.loading = false
        const { data } = res||[];
        let pets = [];
        for (let item of data) {        
          pets = [
            ...pets,
            ...item.pets?.map(pet => {
                return {
                  ...pet,
                  ownerGender: item.gender
                }
            })||[]
          ]
        }
        pets.sort((a,b)=>a.name.localeCompare(b.name))
        this.catListByGender = [
          {
            gender:"Male",
            catList:pets.filter(pet => {
              return pet.type==='Cat'&&pet.ownerGender === 'Male'
            })
          },
          {
            gender:"Female",
            catList:pets.filter(pet => {
              return pet.type==='Cat'&&pet.ownerGender === 'Female'
            })
          }  
        ]   
      } catch (e) {
        console.error('pets加载错误', e)
      }
    }
  },
  mounted(){
    this.renderList();
  }
}
</script>
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: block;
  margin: 0 10px;
}
li:hover{
  font-size: 18px;
}
.loading{
  height: 200px;
  line-height: 200px;
}
</style>
