<template>
  <section class="container">
    <div class="search">
      <input type="text" v-model="search" placeholder="введите имя"/>
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
              <td width="140px;">{{item.name}}</td>
              <td width="140px;">{{item.lastname}}</td>
              <td width="140px;">{{item.profession}}</td>
              <td width="60px;">{{item.age}}</td>
              <td width="260px;">
                <div class="table-box__item-wrap">
                  <div class="table-box__item">
                    <label  class="table-box__item-label" for="newMail">{{item.mail}}</label>
                    <input class="table-box__item-input" type="text" name="" id="newMail" v-model="newMail" :placeholder="item.mail" :class="{ disabled: isActiveMail !== item.id }" >
                  </div>       
                  <button @click="changeMail(item.id)" class="table-box__btn table-box__btn-change">	 </button>
                  <button @click="saveMail(item.id, item.name, item.lastname, item.profession, item.age, item.mail, item.password)" class="table-box__btn table-box__btn-save"></button>
                </div> 
              </td>
              <td width="260px;" >
                <div class="table-box__item-wrap">
                  <div class="table-box__item">
                    <label class="table-box__item-label" for="newPassword">{{item.password}}</label>
                    <input class="table-box__item-input" type="text" name="" id="newPassword" v-model="newPassword" :placeholder="item.password" :class="{ disabled: isActivePassword !== item.id }" >
                  </div>       
                  <button @click="changePassword(item.id)" class="table-box__btn table-box__btn-change"></button>
                  <button @click="savePassword(item.id, item.name, item.lastname, item.profession, item.age, item.mail, item.password)" class="table-box__btn table-box__btn-save"></button>
                </div>
                
              </td>
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
      isActiveMail: false,
      isActivePassword: false,
      newMail: '',
      newPassword: '',
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
      changeMail(id){
        this.isActiveMail = id
      },
      changePassword(id){
        this.isActivePassword = id
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
          this.getItem(); // выглядит как костыль
        })
        .catch((error) => {
          console.log(error.response.data);
        });
        this.isActiveMail = id = false
      },
      savePassword(id, name, lastname, profession, age, mail, password){
        password = this.newPassword
        const updatePassword = {
          'id': id,
          'name': name,
          'lastname': lastname,
          'profession': profession,
          'age': age,
          'mail': mail,
          'password': password
        }

        axios.patch(`http://localhost:3001/items/${id}`, updatePassword)
        .then((response) => {
          console.log(response)
          console.log(updatePassword)
          this.getItem(); // выглядит как костыль
        })
        .catch((error) => {
          console.log(error.response.data);
        });
        this.isActivePassword = id = false
          
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

*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.container{
  max-width: 90vw;
  margin-left: auto;
  margin-right: auto;
}/*поиск*/
.search{
  max-width: 500px;
  margin: 24px auto;
}
.search input{
    box-shadow: 0px 3px 12px rgb(0 0 0 / 13%);
    border-radius: 6px;
    height: 49px;
    width: 490px;
    color: #000000;
    border: none;
    outline: none;
    padding: 0 0 0 16px;
}
.search input::placeholder{
  color: #999;
  font-size: 15px;
}

.search input:focus::-webkit-input-placeholder {
  color: transparent;
}

/*таблица*/
.table-box{
  display: flex;
  flex-direction: column;
}
.table-box thead tr{
  background: #e6f3fa;
}
.table-box tr th:first-child, .table-box tr td:first-child{
  border-top-left-radius: 6px;
  border-bottom-left-radius: 6px;
}
.table-box tr th:last-child, .table-box tr td:last-child {
  border-top-right-radius: 6px;
  border-bottom-right-radius: 6px;
}
.table-box tbody tr:nth-child(even){
  background: #f0f0f0;
}
.table-box th {
  font-weight: 600;
  font-size: 16px;
  line-height: 18px;
  color: #000;
  height: 40px;
  text-align: center;
}
.table-box td{
  font-weight: 400;
  font-size: 16px;
  line-height: 18px;
  height: 40px;
  padding-left: 12px;
}
.table-box__item-wrap{
  display: flex;
}
.table-box__item{
  flex: 1;
  display: flex; 
  position: relative;
  margin-right: 12px;
}
.table-box__item label{
  width: 100%;
  height: 26px;
  position: absolute;
  top: 0;
  left: 0;
  right:0;
  bottom:0;
  font-size: 15px;
  display: flex;
  align-items: center;
}
.table-box__item input{
  width: 100%;
  height: 26px;
  position: absolute;
  top: 0;
  left: 0;
  right:0;
  bottom:0;
  background: white;
  box-shadow: 0px 3px 12px rgb(0 0 0 / 13%);
  border-radius: 6px;
  color: #000000;
  border: none;
  outline: none;
  transform: translateX(-6px);
  padding: 0 6px;
}
.table-box__item input::placeholder{
  font-size: 15px;
  font-family: 'Times New Roman', Times, serif;
}
.table-box__item input:focus::-webkit-input-placeholder {
  color: transparent;
}
.table-box__btn{
  width: 42px;
  height: 26px;
  padding: 6px;
  background-size: 50%;
  background-repeat: no-repeat;
  background-position: center;
  outline: none;
  border-radius: 6px;
  background-color: #ffdf37;
  cursor: pointer;
}
.table-box__btn-change{
  background-image: url('./assets/images/edit.png');
}
.table-box__btn-save{
  background-image: url('./assets/images/save.png');
}

/*навигация */
.pagination{
  max-width: 500px;
  margin: 0 auto;
}
.btn{
  border: none;
  outline: none;
  background: #0288d1;
  cursor: pointer;
  margin: 12px;
  width: 40px;
  height: 40px;
  color: white;
  border-radius: 6px;
}
.btn:disabled{
  background: gray;
  cursor: default;
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



/*js*/

.disabled {
  pointer-events: none;
  background: transparent;
  outline: none;
  border: none;
  visibility: hidden;
}



</style>
