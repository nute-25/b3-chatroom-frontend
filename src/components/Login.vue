<template>
    <div class="login">


        <!--ERREURS-->
        <ul>
            <li v-for="error in errorsArray" v-bind:key="error" class="errors">
                {{ error }}
            </li>
        </ul>


        <div v-if="!user">
            <!--CONNEXION-->
            <form @submit="sendLogin">
                <fieldset>
                    <legend>Login</legend>
                    <label for="login">login</label>
                    <input type="text" name="login" id="login" v-model="userLogin" placeholder="Enter your login">
                    <label for="password">password</label>
                    <input type="password" name="login" id="password" v-model="userPassword" placeholder="Enter your password">
                </fieldset>
                <input type="submit" value="Login">
            </form>
        </div>


        <div v-else>
            <h2>User connecté : {{ user.handle }} </h2>
            <!--CHATROOM-->
            <form @submit="createChatroom">
                <fieldset>
                    <legend>Chatroom register</legend>
                    <label for="chatroomTitle">title</label>
                    <input type="text"  name="title" id="chatroomTitle" v-model="chatroomTitle" placeholder="Enter chatroom's title">
                </fieldset>
                <input type="submit" value="Create">
            </form>
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

                chatroom: false,
                chatroomTitle: '',

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
                        /*console.log(JSON.stringify(myJson));
                        console.log(myJson['id']);*/
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
            createChatroom : function(e) {
                let self = this;
                let data = {
                    title: this.chatroomTitle,
                    user_id: this.user.id
                };
                // console.log(data);

                // appel fetch
                fetch('http://localhost/b3-chatroom-backend/controllers/chatrooms_controller.php?action=register', {
                    method: 'POST', // or 'PUT'
                    body: JSON.stringify(data), // data can be string or {object}!
                    headers:{
                        'Content-Type': 'application/json'
                    }
                })
                    .then(function(response) {
                        // reponse retournee par le backend php sous la forme de promesse
                        // lecture données et parsing json()
                        return response.json();
                    })
                    .then(function(myJson) {
                        // recupération des valeurs retournées dans la promesse (ex: false ou erreurs à afficher si les champs requis pas bons)
                        // console.log(JSON.stringify(myJson));
                        // si une chatroom a été enregistré
                        if (myJson.title) {
                            // on enregistre ses données
                            self.chatroom = myJson;
                            // on vide le tableau d'erreurs
                            self.errorsArray = [];
                        }
                        else {
                            // sinon on affiche les erreurs lui expliquant pourquoi il n'a pas pu créer une chatroom
                            self.errorsArray = myJson;
                        }
                    });
                e.preventDefault();
            }
        }
    }
</script>

<style scoped>
    form {
        width: 400px;
    }
</style>