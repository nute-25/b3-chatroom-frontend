<template>
    <div class="login">

        <!--formulaire de login-->
        <div v-if="!user">
            <ul>
                <li v-for="error in errorsArray" v-bind:key="error" class="errors">
                    {{ error }}
                </li>
            </ul>

            <form @submit="sendLogin">
                <fieldset>
                    <legend>Login</legend>
                    <label>login</label>
                    <input type="text" name="login" v-model="userLogin" placeholder="Enter your login">
                    <label>password</label>
                    <input type="password" name="login" v-model="userPassword" placeholder="Enter your password">
                </fieldset>
                <input type="submit" value="login">
            </form>
        </div>

        <div v-else>
            <h2>User connecté : {{ user.handle }} </h2>
        </div>


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
            sendLogin: function (e) {
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
                        console.log(myJson['id']);
                        // si un user s'est connecté
                        if (myJson.login) {
                            // on enregistre ses données utilisateur
                            self.user = myJson;
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
    form {
        width: 400px;
    }
</style>