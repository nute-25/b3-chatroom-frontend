<template>
    <div class="login">
        <ul>
            <li v-for="error in errorsArray">
                {{ error }}
            </li>
        </ul>
        <h1>Login</h1>
        <form @submit="sendMessage">
            <label>login</label>
            <input type="text" name="login" v-model="userLogin" placeholder="Enter your login">
            <label>password</label>
            <input type="password" name="login" v-model="userPassword" placeholder="Enter your password">
            <input type="submit" value="login">
        </form>

    </div>
</template>

<script>
    export default {
        name: "Login",
        data() {
            return {
                user: false,
                userLogin: '',
                userPassword: '',
                errorsArray: []
            }
        },
        methods : {
            sendMessage: function (e) {
                let self = this;
                let data = {
                    login: this.userLogin,
                    password: this.userPassword
                };
                // console.log(data);

                fetch('http://localhost/b3-chatroom-backend/controllers/users_controller.php?action=login', {
                    method: 'POST', // or 'PUT'
                    body: JSON.stringify(data), // data can be string or {object}!
                    headers:{
                        'Content-Type': 'application/json'
                    }
                })
                    .then(function(response) {
                        // reponse retournee par le backend
                        return response.json();
                    })
                    .then(function(myJson) {
                        // reponse que l on se renvoie en php (ex: false si login pas bon)
                        console.log(JSON.stringify(myJson));
                        //myJson['']
                        self.errorsArray = myJson;
                    });
                e.preventDefault();
            },
        }
    }
</script>

<style scoped>

</style>