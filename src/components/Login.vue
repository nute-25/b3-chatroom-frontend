<template>
    <div class="login" v-if="!user">
        <ul>
            <li v-for="error in errorsArray" v-bind:key="error">
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

                // appel fetch
                fetch('http://localhost/b3-chatroom-backend/controllers/users_controller.php?action=login', {
                    method: 'POST', // or 'PUT'
                    body: JSON.stringify(data), // data can be string or {object}!
                    headers:{
                        'Content-Type': 'application/json'
                    }
                })
                    .then(function(response) {
                        // console.log(response.json());
                        // reponse retournee par le backend php sous la forme de promesse
                        // lecture données et parsing json()
                        return response.json();
                    })
                    .then(function(myJson) {
                        // recupération des valeurs retournées dans la promesse (ex: false ou erreurs à afficher si login pas bon)
                        console.log(JSON.stringify(myJson));
                        console.log(myJson[0].id);
                        // si un user s'est connecté
                        if (myJson[0].login) {
                            self.user = true;
                            // on vide le tableau d'erreurs
                            self.errorsArray = [];
                        }
                        else {
                            // self.user = false;
                            // sinon on affiche les erreurs lui expliquant pourquoi il n'a pas pu se logguer
                            self.errorsArray = myJson;
                        }

                    });
                e.preventDefault();
            },
        }
    }
</script>

<style scoped>

</style>