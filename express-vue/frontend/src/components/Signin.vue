<template>
    <div class="modal">
        <div class="form">
            <h1 class="inputhead">Signin</h1>
            <hr>
            <div class="input">
                <label>ID</label>
                <input @keyup.esc="cancel" @keyup.enter="signin()" class="id" type="text" v-model="id" placeholder="ID" autofocus />
            </div>
            <div class="input">
                <label>Password</label>
                <input @keyup.esc="cancel" @keyup.enter="signin()" class="password" type="password" v-model="password" placeholder="Password" /> 
            </div>
            <div class="input">
                <button @click="signin()">Signin</button>
                <button @click="cancel()">Cancel</button>
            </div>
        </div>
    </div>
</template>

<script>
import { mapState } from 'vuex';
import constant from '../constant.js';
import contactapi from '../contactapi.js';

export default {
    name: "login",
    data: function() {
        return {
            id: "",
            password: "",
            isWrong: false
        }
    },
    computed: mapState([ "IDs" ]),
    watch: {
        id: function() {
            if(this.id.length > 10) {
                alert("ID can't be longer then 10 digits");
                this.id.slice(0, -1);
            }
        }
    },
    methods: {
        signin: function() {
            if(this.id.length < 3) {
                return alert("ID must be longer then 3 digits");
            }
            else {
                let words = "";
                let error;
                contactapi.signin(this.id, this.password)
                .then((res) => {
                    words = res.data.words;
                    error = res.data.error;
                    if(res.data.error == "true") {
                        console.log(words);
                    }
                    else {
                        console.log(res.data);
                    }
                    if(error == "false") {
                        this.$store.dispatch(constant.SIGNIN, { id: this.id, password: this.password });
                        this.id = "";
                        this.password = "";
                        console.log("get signin");
                        return this.$router.push({ name: 'home' });
                    }
                    else {
                        return alert(words);
                    }
                });
            }
        },
        cancel: function() {
            this.$router.push({ name: 'home' });
        }
    }
}
</script>

<style scoped>
    .modal {
        display: block; position: fixed; width: 100%; height: 100%;
        left: 0; top: 0; overflow: auto; z-index: 1;
        background: rgb(0, 0, 0); background: rgba(0, 0, 0, 0.4);
    }
    .form {
        background-color: white; margin: 100px auto;
        max-width: 500px; min-width: 200px;
        font: 13px "verdana"; padding: 10px 10px 10px 10px;
    }
    button {
        width: 100px; height: 40px; background: white;
    }
    button:hover {
        background: black; color: white;
    }
    .form label {
        text-align: left; margin: 0 0 3px 0; padding: 0;
        display: black; font-weight: bold;
    }
    .form input {
        box-sizing: border-box; outline: none;
    }
    .inputhead {
        background: black; color: white; font-weight: bold;
        width: 400px; height: 40px; text-align: left;
    }
</style>
