<template>
  <form>
    <loader v-if="loading.groups || loading.membership"></loader>
    <template v-else>
   <div class="col s12" ><!--:test=test -->
      <div class= "row">
        <center>
          <h4>Novi korisnik</h4>
        </center>
      </div>
      <div class="row" v-if="errorsArray">
        <div class="danger" v-for="errorMessage in errorsArray">
          <p><strong>Oprez!</strong> {{errorMessage}}</p>
      </div>
      </div>

      <div class="tab">
              <button class="tablinks" v-on:click="openEvent($event,'obavezneInformacije')">Obavezne informacije</button>
              <button class="tablinks" v-on:click="openEvent($event,'dodatneInformacije')">Dodatne informacije</button>
      </div>

      <div id="obavezneInformacije" class="tabcontent">
        <h3>Obavezne informacije</h3>

        <div class="row">
        <div class="input-field col s6">
          <input type="text" class="validate" v-model="newUserObject.user.first_name">
          <label  for="Ime">Ime</label>
        </div>
        <div class="input-field col s6">
          <input id="Prezime" type="text" class="validate" v-model="newUserObject.user.last_name">
          <label for="Prezime">Prezime</label>
        </div>
      </div>

      <div class="row">
        <div class="input-field col s6">
          <input id="cardNumber" type="text" class="validate" v-model="newUserObject.user.code">
          <label for="cardNumber">Broj kartice</label>
        </div>
        <div class="input-field col s6">
          <label class="active" for="aktivnost">Aktivnost</label>
          <br />
          <v-select v-model="statusSelect" :options="[{ label: 'Aktivni', value: 'active'},{ label: 'Neaktivni', value: 'inactive'}, { label: 'Pauza', value: 'pause'}]"></v-select>
        </div>
      </div>
      <div class= "row">
        
        <div class="field col s12">
          <label class="active" for="memberships">Članarine</label>
          <br />
          <v-select multiple v-model="membershipOption" :options="membershipsForPick"></v-select>
        </div>
      </div>
      <div class="row">
        <div class="field col s12">
          <label class="active" for="groups">Grupe</label>
          <br />
          <v-select multiple v-model="groupOption" :options="groupsForPick"></v-select>
        </div>
      </div>

      </div>

      <div id="dodatneInformacije" class="tabcontent">
          <h3>Dodatne informacije</h3>
          <div class="row">
        <div class="input-field col s12">
          <input id="Address" type="text" class="validate" v-model="newUserObject.user.address">
          <label  for="Address">Adresa</label>
        </div>
        <!--<div class="input-field col s6">
          <input id="OIB" type="text" class="validate" v-model="newUserObject.user.OIB">
          <label for="OIB">OIB</label>
        </div> -->
        </div>
        <div class="row">
        <div class="input-field col s6">
          <input  id="birthDate" type="date" v-model="newUserObject.user.birth_date">
          <label class="active" for="birthDate">Datum rođenja</label>
        </div>
       <div class="input-field col s6">
          <input id="phoneNumber" type="text" class="validate" v-model="newUserObject.user.phone_number">
          <label  for="phoneNumber">Tel</label>
        </div>
      </div>
      <div class="row">
        <div class="input-field col s6">
          <input id="Email" type="text" class="validate" v-model="newUserObject.user.email">
          <label for="Email">Email</label>
        </div>
        <div class="input-field col s6">
          <label class="active" for="gender">Spol</label>
          <br />
          <v-select v-model="genderSelect" :options="[{ label: 'M', value: 'm'},{ label: 'Ž', value: 'ž'}]"></v-select>
        </div>
      </div>
      
      </div>







      
      
      
      

      <div class="row">
        
        
      </div>

      <div class="row">
        
      </div>
      
      <div class="row">
        <div class="input-field col s6">
          <input  id="from" type="date" v-model="newUserObject.user.membership_starts_at">
          <label class="active" for="from">Trajanje članarine od:</label>
        </div>
        <div class="input-field col s6">
          <input  id="to" type="date" v-model="newUserObject.user.membership_ends_at">
          <label class="active" for="to">Trajanje članarine do:</label>
        </div>
      </div>
      <div class="row">
        <div class="field col s12">
          <center>
          <a class="waves-effect waves-light btn" type="submit" value="Submit" v-on:click="createNewUser" >Kreiraj novog korisnika</a>
        </center>
        </div>
      </div>

    </div>
  </template>
</form>
</template>


<script>
import session from '../Session.js'
import moment from 'moment'
import Loader from './Loader.vue'
export default {
  name: 'newUser',
  data(){
    return {
      visible: false,
      isActive: false,
      error: false,
      newUserObject: {
        user: {
          id: '',
          first_name: '',
          last_name: '',
          birth_date: '',
          email: '',
          status: '',
          birth_date: '',
          OIB: '',
          phone_number: '',
          address: '',
          sex: '',
          code: '',
          membership_type_ids: [],
          group_ids: [],
          membership_starts_at: '',
          membership_ends_at: '',
          membership_pause_at: ''
        }
      },
      memberships: [],
      membershipsForPick: [],
      membershipOption: '',
      groups: [],
      groupsForPick: [],
      groupOption: '',
      statusSelect: '',
      genderSelect: '',
      errorsArray: [],
      error: false,
      loading: {
        membership: false,
        groups: false
      },
      i: 0,
      flag: 0

    }
  },
  methods:{
    openEvent(evt, nameTab){
     
      var i, tabcontent, tablinks;

    // Get all elements with class="tabcontent" and hide them
    tabcontent = document.getElementsByClassName("tabcontent");
    for (i = 0; i < tabcontent.length; i++) {
        tabcontent[i].style.display = "none";
    }
   
    // Get all elements with class="tablinks" and remove the class "active"
    tablinks = document.getElementsByClassName("tablinks");
    for (i = 0; i < tablinks.length; i++) {
        tablinks[i].className = tablinks[i].className.replace(" active", "");
    }
    
    // Show the current tab, and add an "active" class to the button that opened the tab
    document.getElementById(nameTab).style.display = "block";
     
    },
    
    createNewUser(){
      this.errorsArray=[];
      if(!this.newUserObject.user.first_name) this.errorsArray.push("Potrebo je upisati ime korisnika.");
      if(!this.newUserObject.user.last_name) this.errorsArray.push("Potrebo je upisati prezime korisnika.");
      if(!this.newUserObject.user.code) this.errorsArray.push("Potrebo je zapisati korisnikovu karticu.");
      if(!this.statusSelect) this.errorsArray.push("Potrebo je odabrati aktivnost korisnika.");
      if(!this.membershipOption || this.membershipOption.length == 0) this.errorsArray.push("Potrebo je odabrati članarinu korisnika.");
      //if(!this.groupOption || this.groupOption.length == 0) this.errorsArray.push("Potrebo je odabrati grupu korisnika.");
      if(this.errorsArray.length == 0){
        var self = this;
        if(this.membershipOption){
        this.membershipOption.forEach(function(el){
          self.newUserObject.user.membership_type_ids.push(el.value);
        });
        }
        if(this.groupOption){
        this.groupOption.forEach(function(el){
          self.newUserObject.user.group_ids.push(el.value);
        });
        }
        if(this.statusSelect){
          this.newUserObject.user.status = this.statusSelect.value;
          if(this.statusSelect.value=='pause'){
            this.newUserObject.user.membership_pause_at = moment().format('YYYY-MM-DD');
          }else {
            this.newUserObject.user.membership_pause_at ='';
          }
        }
        if(this.genderSelect){
          this.newUserObject.user.sex = this.genderSelect.value;
        }
       this.$http.post(this.$callHttp + '/api/v1/users/create', this.newUserObject).then(response => {
          // success callback
          this.error = false;
          return response.json();
        }, error => {
          // error callback
          if(error.status){
            console.log(`Došlo je do pogreške ${error.status}`);
            this.error = true;
        }
        }).then(data => {
          if(data.status=='401')session.sessionDestroy();
          //obrada podataka
          this.newUserObject.user.id=data.user.id;
          location.reload();
          
        });

      }
    }
  },
  components: {
    Loader
  },
  created(){
    //
      this.loading.membership = true;
      this.loading.groups = true;
    this.$http.get(this.$callHttp +'/api/v1/membership_types/index').then(response => {
      // success callback
      this.loading.membership = false;
      return response.json();
    }, error => {
      // error callback
      if(error.status){
        console.log(`error is ${error.status}`);
        this.error = true;
      }
    }).then(data => {
      if(data.status=='401')session.sessionDestroy();
      //obrada podataka
      this.memberships= data.membership_types;
      var self = this;
      data.membership_types.map(x => {
        let obj = {
          label: x.name,
          value: x.id
        }
        self.membershipsForPick.push(obj);
      });
    });


    this.$http.get(this.$callHttp +'/api/v1/groups/index').then(response => {
      // success callback
      this.loading.groups = false;
      return response.json();
    }, error => {
      if(error.status){
        console.log(`error is ${error.status}`);
        this.error = true;
      }
      // error callback
    }).then(data => {
      //obrada podataka
      if(data.status=='401')session.sessionDestroy();
      this.groups = data.groups;
      var self = this;
      data.groups.map(x => {
        let obj = {
          label: x.name,
          value: x.id
        }
          self.groupsForPick.push(obj);
      });
    });

    this.newUserObject.user.membership_starts_at = moment().format('YYYY-MM-DD');
    this.newUserObject.user.membership_ends_at = moment().add(1, 'M').format('YYYY-MM-DD');
    
  },
  mounted(){
    var self = this;
    setTimeout(function() { var evt = new MouseEvent("click");
    self.openEvent(evt, 'obavezneInformacije'); }, 300);
    
  }
}

</script>

<style lang="scss" scoped>

.sizeOf{
  font-size: 25px;
}

.danger {
    background-color: #ffdddd;
    border-left: 6px solid #f44336;
}
.tab {
    overflow: hidden;
    border: 1px solid #ccc;
    background-color: #f1f1f1;
}

/* Style the buttons inside the tab */
.tab button {
    background-color: inherit;
    float: left;
    border: none;
    outline: none;
    cursor: pointer;
    padding: 14px 16px;
    transition: 0.3s;
    font-size: 17px;
}

/* Change background color of buttons on hover */
.tab button:hover {
    background-color: #ddd;
}

/* Create an active/current tablink class */
.tab button.active {
    background-color: #ccc;
}

/* Style the tab content */
.tabcontent {
    display: none;
    padding: 6px 12px;
    border: 1px solid #ccc;
    border-top: none;
}

</style>
