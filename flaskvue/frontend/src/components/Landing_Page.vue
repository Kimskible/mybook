/* eslint-disable vue/valid-v-model */
<template>
<div class = "container-fluid">
<nav class="navbar navbar-expand-lg   fixed-top navbar-light bg-success">
  <a class="navbar-brand ml-2" href="#">
  <span style = "color:white;font-size:32px;font-weight:bold">MyBook</span>
  </a>
  <div class="collapse navbar-collapse" id="navbarSupportedContent">
    <form v-on:submit.prevent="login" class="form-inline my-2 my-lg-0 ml-auto">
      <input v-model = "login_email" name = "email" class="form-control sm-1 mr-sm-2" type="text" placeholder="E-mail" aria-label="Search">
      <input v-model ="login_password" name ="login-password" class="form-control sm-1 mr-sm-2" type="password" placeholder="Password" aria-label="Search">
      <button   class="btn btn-success btn-outline-white my-2 my-sm-0" type="submit">Login</button>
    </form>
  </div>
</nav>

<div class = "container-fluid" style = "margin-top:10%">
  <div class = "row mt-5">
    <div class = "col mb-5">
      <div class = "container-fluid">
        <div class = "row">
         <div class = "col">
            <p style = "font-size:32px;font-weight:bold;text-align:left">Connect with friends and the world around you on MyBook</p>
          </div>
        </div>
        <div class = "row">
          <div class = "col">
            <div class = "row">
              <div class = "d-flex p-0 col">
              <img src = "../assets/assignment-24px.png" class = "img-fluid mr-3" style = "align-self:left">
              <h4 style = "font-size:20px;font-weight:bold;align-self:center">See photos and updates<span style = "font-weight:normal;"> from friends in News Feed.</span></h4>
              </div>
            </div>
            <div class = "row">
              <div class = "d-flex p-0 col">
              <img src = "../assets/tv-24px.png" class = "img-fluid mr-4" style = "align-self:left">
              <h4 style = "font-size:20px;font-weight:bold;align-self:center">Share what's new<span style = "font-weight:normal;"> in your life on your Timeline.</span></h4>
              </div>
            </div>
            <div class = "row">
              <div class = "d-flex p-0 col">
              <img src = "../assets/share-24px.png" class = "img-fluid mr-4" style = "align-self:center">
              <h4 style = "font-size:20px;font-weight:bold;align-self:center">Find more<span style = "font-weight:normal;"> of what you're looking for with MyBook Search.</span></h4>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class = "col">
      <div class = "container container-sm-1">
        <div class = "row">
         <div class = "col">
            <p style = "font-size:32px;font-weight:bold;text-align:left">Sign Up</p>
            <p style = "font-size:26px;font-weight:normal;text-align:left">It's quick and easy</p>
            <form v-on:submit.prevent = "register">
                <div class = "row mb-3 ">
                  <div class = "col-sm-4">
                    <input name = "fname" v-model ="fname" class="form-control sm-1" type="text" placeholder="First Name" aria-label="first name">
                  </div>
                  <div class = "col-sm-4">
                    <input name = "lname"  v-model= "lname" class="form-control sm-1" type="text" placeholder="Last Name" aria-label="last name">
                  </div>
                </div>
                <div class = "row mb-3">
                  <div class = "col-lg-8">
                      <input name = "email"  v-model="email" class="form-control sm- mr-sm-2" type="email" placeholder="E-mail" aria-label="email">
                  </div>
                </div>
                <div class = "row mb-3">
                  <div class = "col-lg-8">
                      <input  name = "password"  v-model = "password" class="form-control sm- mr-sm-2" type="password" placeholder="Password" aria-label="password">
                  </div>
                  </div>
                <div class = "row no-gutters justify-content-start mb-3">
                    <button value = "register_button" name = "register_button" style="background-color:#24416D;color:white;font-weight:bold;letter-spacing:2px;" class="btn btn-outline-white my-2 my-sm-0" type="submit">Sign Up</button>
                </div>
            </form>
            <FlashMessage></FlashMessage>
           </div>
          </div>
        </div>
      </div>
    </div>
  </div>
  <nav style = "z-index:-1" class="navbar navbar-expand-lg p-4 fixed-bottom navbar-light bg-success">
    <h4 class = "ml-auto nav-brand" style = "color:white">Copyright 2020 COMP3161</h4>
  </nav>
</div>
</template>

<script>
import axios from 'axios'
import router from '../router'
import EventBus from './EventBus'

export default {
  data () {
    return {
      email: '',
      password: '',
      fname: '',
      lname: '',
      form: ''
    }
  },

  methods: {
    register () {
      axios.post('http://localhost:5000/register', {
        email: this.email,
        password: this.password,
        fname: this.fname,
        form: 'register',
        lname: this.lname
      }).then((res) => {
        this.flashMessage.success({title: 'Registration Successful', message: 'New user created, please try logging in'})
      }).catch((err) => {
        // eslint-disable-next-line eqeqeq
        if (err.name != 'NavigationDuplicated') {
          this.flashMessage.error({title: err.name, message: err.message})
          throw err
        }
      })
      var x
      for (x of document.getElementsByClassName('form-control')) {
        x.value = ''
      }
    },
    login () {
      axios.post('http://localhost:5000/login', {
        email: this.login_email,
        password: this.login_password,
        form: 'login'
      }).then((res) => {
        localStorage.setItem('usertoken', res.data)
        this.email = ''
        this.password = ''
        router.push({ name: 'Home' })
        this.flashMessage.success({title: 'Log In Successful', message: "You've successfully Logged In!"})
      }).catch((err) => {
        // eslint-disable-next-line eqeqeq
        if (err.name != 'NavigationDuplicated') {
          this.flashMessage.error({title: 'Invalid Login', message: 'Either the username or password is invalid'})
          console.log(err)
          throw err
        }
      })
      this.emitMethod()
    },
    emitMethod () {
      EventBus.$emit('logged-in', 'loggedin')
    }
  }
}
</script>
