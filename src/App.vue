<template>
  <section class="container">
    <div class="search">
      <input type="text" v-model="search" placeholder="поиск по имени"/>
    </div>
    <div class="table-box">
      <table>
        <thead>
          <tr>
              <th>Имя</th>
              <th>Фамилия</th>
              <th>Профессия</th>
              <th>Возраст</th>
              <th>Почта</th>
              <th>Пароль</th>
          </tr>
        </thead>
        <tbody >
          <tr v-for="(item) in paginatedData" :key="item.id">
              <td>{{item.name}}</td>
              <td>{{item.lastname}}</td>
              <td>{{item.profession}}</td>
              <td>{{item.age}}</td>
              <td class="table-box__mail-wrap">
                <div class="table-box__mail">
                  <label  class="table-box__mail-label" for="newMail">{{item.mail}}</label>
                  <!-- <label v-if="showNewMail" class="table-box__mail-label" for="newMail" :placeholder="item.newMail">{{this.newMail}}</label> -->
                  <input class="table-box__mail-input" type="text" name="" id="newMail" v-model="newMail" :placeholder="item.mail" :class="{ disabled: isActiveInput !== item.id }" >
                </div>       
                <button @click="changeItem(item.id)">изменить</button>
                <button @click="saveMail(item.id, item.name, item.lastname, item.profession, item.age, item.mail, item.password)">сохранить</button>
              </td>
              <td>{{item.password}}</td>
          </tr>
        </tbody> 

      </table> 
      <div class="pagination">
        <button @click="prevPage" :disabled="pageNumber === 0" class="btn">left</button>
        <button @click="nextPage" :disabled="pageNumber >= pageCount - 1" class="btn">right</button>
      </div> 
    </div>
    <div class="form__box">
      <h1>Добавить сотрудника</h1>  
      <form class="form">
        <div class="form__item">
          <label for="name">Name</label>
          <input type="text" name="" id="name" v-model="name">
          <span>{{this.name}}</span>
        </div>
        <div class="form__item">
          <label for="lastname">LastName</label>
          <input type="text" name="" id="lastname" v-model="lastname">
        </div>
        <div class="form__item">
          <label for="profession">Profession</label>
          <input type="text" name="" id="profession" v-model="profession">
        </div>
        <div class="form__item">
          <label for="age">Age</label>
          <input type="number" name="" id="age" v-model="age">
        </div>
        <div class="form__item">
          <label for="mail">Mail</label>
          <input type="mail" name="" id="mail" v-model="mail">
        </div>
        <div class="form__item">
          <label for="password">Password</label>
          <input type="password" name="" id="password" v-model="password">
        </div>
        <button @click.prevent="postItem" class="btn">add new Item</button>
      </form>
    </div>
  </section>
</template>

<script>
import axios from 'axios';
export default {
  name: 'App',
  data(){
    return {
      items: [],
      name: '',
      lastname: '',
      profession: '',
      age: null,
      mail: '',
      password: '',
      isActiveInput: false,
      newMail: '',
      pageNumber: 0,
      size: 5,
      search:'',
    }
  },
  mounted(){
    this.getItem();
    
  },
  watch: {
  },
  computed: {
    pageCount() {
      return Math.ceil(this.items.length / this.size);
    },
    paginatedData() {
      return this.searchData.slice(
        this.pageNumber * this.size,
        this.pageNumber * this.size + this.size
      );
    },
    searchData() {
      return this.items.filter((item) => {
        return item.name.toLowerCase().includes(this.search.toLowerCase());
      });
    },
  },
  methods: {
      getItem(){
     axios
      .get('http://localhost:3001/items')
      .then(response => (this.items = response.data));
      },
    
      postItem() {
        const newItem = {
          'id': Date.now(),
          'name': this.name,
          'lastname': this.lastname,
          'profession': this.profession,
          'age': this.age,
          'mail': this.mail,
          'password': this.password
        }
        axios.post('http://localhost:3001/items', newItem)
          .then((response) => {
            console.log(response);
            console.log(newItem)
          })
          .catch((error) => {
            console.log(error.response.data);
          });
          this.items.push(newItem)
      },
      changeItem(id){
        this.isActiveInput = id
      },
      saveMail(id, name, lastname, profession, age, mail, password){
        mail = this.newMail
        const updateMail = {
          'id': id,
          'name': name,
          'lastname': lastname,
          'profession': profession,
          'age': age,
          'mail': mail,
          'password': password
        }

         axios.patch(`http://localhost:3001/items/${id}`, updateMail)
          .then((response) => {
            console.log(response)
            console.log(updateMail)
          })
          .catch((error) => {
            console.log(error.response.data);
          });
          // this.$forceUpdate(); // выглядит как костыль, разобраться как не делать принудительную перезагрузку
      },
      nextPage() {
        this.pageNumber++;
      },
      prevPage() {
        this.pageNumber--;
      },
      
  }
}
</script>

<style>
.container{
  max-width: 90vw;
  margin-left: auto;
  margin-right: auto;
}
.search{
  max-width: 500px;
  margin: 24px auto;
}
.search input{
  height: 30px;
  width: 100%;
  border: 1px solid skyblue;
  outline: 1px solid skyblue;
}
.table-box{
  display: flex;
  flex-direction: column;
}
.form__box{
  max-width: 500px;
  margin: 24px auto;
}

.form__box h1 {
  text-align: center;
}
.form{
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.form__item{
  width: 100%;
  display: flex;
  justify-content:flex-start;
  align-items: center;
}
.form__item label{
  min-width: 120px;
}
.form__item input{
  flex: 1;
  border: 1px solid skyblue;
  outline: 1px solid skyblue;
  height: 20px;
}
.pagination{
  max-width: 500px;
  margin: 0 auto;
}
.btn{
  border: none;
  outline: none;
  background: skyblue;
  cursor: pointer;
  margin: 12px;
  padding: 12px 24px;
}



.disabled {
  pointer-events: none;
  background: transparent;
  outline: none;
  border: none;
  visibility: hidden;
}


</style>
