<template>
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
        <tr v-for="(item) in items" :key="item.id">
            <td>{{item.name}}</td>
            <td id="last">{{item.lastname}}</td>
            <td>{{item.profession}}</td>
            <td>{{item.age}}</td>
            <td class="table-box__mail-wrap"> 
              <div class="table-box__mail">
                <label class="table-box__mail-label" for="newMail">{{item.mail}}</label>
                <input class="table-box__mail-input" type="text" name="" id="newMail" v-model="newMail" :placeholder="item.mail" :class="{ disabled: isActiveInput !== item.id }" >
              </div>       
              <button @click="changeItem(item.id)">изменить</button>
              <button @click="saveMail(item.id, item.name, item.lastname, item.profession, item.age, item.mail, item.password)">сохранить</button>
              </td>
            <td>{{item.password}}</td>
        </tr>
      </tbody> 
    </table>  
    <form>
      <span>{{newMail}}</span>
      <div>
        <label for="name">Name</label>
        <input type="text" name="" id="name" v-model="name">
        <span>{{this.name}}</span>
      </div>
      <div>
        <label for="lastname">LastName</label>
        <input type="text" name="" id="lastname" v-model="lastname">
      </div>
      <div>
        <label for="profession">Profession</label>
        <input type="text" name="" id="profession" v-model="profession">
      </div>
      <div>
        <label for="age">Age</label>
        <input type="number" name="" id="age" v-model="age">
      </div>
      <div>
        <label for="mail">Mail</label>
        <input type="mail" name="" id="mail" v-model="mail">
      </div>
      <div>
        <label for="password">Password</label>
        <input type="password" name="" id="password" v-model="password">
      </div>
      <button @click.prevent="postItem">add new Item</button>

    </form>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'App',

  components: {
  },
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
    
    }
  },
  mounted(){
    this.getItem();
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
        this.isActiveInput = id;
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
      }
  }
}
</script>

<style>
  .table-box{
    display: flex;
    border: 1px solid red;
  }
  table{
    width: 50%;
    border: 1px solid red;
  }
  form{
    width: 50%;
    border: 1px solid green;
  }
  .disabled {
    pointer-events: none;
    background: transparent;
    outline: none;
    border: none;
    visibility: hidden;
  }
  /* .table-box__mail {
    position: relative;
    width: 200px;
    height: 20px;
  }
  .table-box__mail-input{
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
  }
  .table-box__mail-input::placeholder{
    z-index: 4;
    background: white;
  }
  .table-box__mail-label{
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    z-index: 2;
    visibility: visible;
  } */
</style>
