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

            <!--MESSAGES-->

            <!--Un utilisateur peut poster un message dans une chatroom-->
            <h3>Add message in Chatroom</h3>
            <form @submit="sendMessage">
                <fieldset>
                    <legend>Send message in Chatroom</legend>
                    <label for="messageContent">Content</label>
                    <textarea name="messageContent" id="messageContent" v-model="messageContent" placeholder="Enter your message"></textarea>
                    <label for="messagechatroom">Chatroom</label>
                    <select name="messagechatroom" id="messagechatroom" v-model="messageChatroom">
                        <option v-for="chatroomData in chatroomDatasArray" v-bind:key="chatroomData.title" :value="chatroomData.id">{{ chatroomData.title }}</option>
                    </select>
                </fieldset>
                <input type="submit" value="Send Message">
            </form>

            <!--Affichage Messages de l'utilisateur-->
            <h3>Messages list of user</h3>
            <table>
                <thead>
                <tr>
                    <th>content</th>
                    <th>created</th>
                    <th>chatroom_id</th>
                    <th>delete</th>
                </tr>
                </thead>
                <tbody>
                <tr v-if="messageDatasArray.length === 0">
                    <td colspan="5">No message found</td>
                </tr>
                <tr v-else v-for="messageData in messageDatasArray" v-bind:key="messageData.content">
                    <td>{{ messageData.content }}</td>
                    <td>{{ messageData.created }}</td>
                    <td>{{ messageData.chatroom_id }}</td>
                    <td><button>delete</button></td>
                </tr>
                </tbody>
            </table>



            <!--CHATROOM-->

            <!--Création Chatroom par un utilisateur-->
            <h3>Create chatroom</h3>
            <form @submit="createChatroom">
                <fieldset>
                    <legend>Chatroom register</legend>
                    <label for="chatroomTitle">title</label>
                    <input type="text"  name="title" id="chatroomTitle" v-model="chatroomTitle" placeholder="Enter chatroom's title">
                </fieldset>
                <input type="submit" value="Create">
            </form>

            <!--Affichage Chatroom de l'utilisateur-->
            <table>
                <thead>
                <tr>
                    <th>title</th>
                    <th>user_id</th>
                    <th>created</th>
                    <th>modified</th>
                    <th>update</th>
                    <th>messages</th>
                </tr>
                </thead>
                <tbody>
                <tr v-if="chatroomDatasArray.length === 0">
                    <td colspan="5">No chatroom found</td>
                </tr>
                <tr v-else v-for="chatroomData in chatroomDatasArray" v-bind:key="chatroomData.title">
                    <td>{{ chatroomData.id }}</td>
                    <td>{{ chatroomData.title }}</td>
                    <td>{{ chatroomData.user_id }}</td>
                    <td>{{ chatroomData.created }}</td>
                    <td>{{ chatroomData.modified }}</td>
                </tr>
                </tbody>
            </table>

            <!--<div v-for="chatroomData in chatroomDatasArray" v-bind:key="chatroomData.title">
                <h3>Message list of {{ chatroomData.title}}</h3>
                <table>
                    <thead>
                    <tr>
                        <th>content</th>
                        <th>created</th>
                        <th>delete</th>
                    </tr>
                    </thead>
                    <tbody>
                    <tr v-if="messageDatasArray.length === 0">
                        <td colspan="5">No message found</td>
                    </tr>
                    <tr v-else v-for="messageData in messageDatasArray" v-bind:key="messageData.content">
                        <td>{{ messageData.content }}</td>
                        <td>{{ messageData.created }}</td>
                        <td><button>delete</button></td>
                    </tr>
                    </tbody>
                </table>
            </div>-->


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

                messageDatasArray: [],

                chatroomTitle: '',
                chatroomDatasArray: [],

                messageContent: '',
                messageChatroom: '',

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
                    .then(function(answer) {
                        // recupération des valeurs retournées dans la promesse (ex: false ou erreurs à afficher si login pas bon)
                        /*console.log(JSON.stringify(answer));
                        console.log(answer['id']);*/
                        // si un user s'est connecté
                        if (answer.login) {
                            // on enregistre ses données utilisateur
                            self.user = answer;
                            // on vide le tableau d'erreurs
                            self.errorsArray = [];
                            self.getMessageUser(e);
                            self.getChatroomUser(e);
                        }
                        else {
                            // self.user = false;
                            // sinon on affiche les erreurs lui expliquant pourquoi il n'a pas pu se logguer
                            self.errorsArray = answer;
                        }
                    });
                e.preventDefault();
            },
            sendMessage : function(e) {
                let self = this;
                let data = {
                    content: this.messageContent,
                    user_id: this.user.id,
                    chatroom_id: this.messageChatroom
                };
                // console.log(data);

                // appel fetch
                fetch('http://localhost/b3-chatroom-backend/controllers/messages_controller.php?action=register', {
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
                    .then(function(answer) {
                        // recupération des valeurs retournées dans la promesse (ex: false ou erreurs à afficher si les champs requis pas bons)
                        // console.log(JSON.stringify(answer));
                        // si une chatroom a été enregistré
                        if (answer === true) {
                            // on vide le tableau d'erreurs
                            self.errorsArray = [];
                            self.getMessageUser(e);
                        }
                        else {
                            // sinon on affiche les erreurs lui expliquant pourquoi il n'a pas pu créer une chatroom
                            self.errorsArray = answer;
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
                    .then(function(answer) {
                        // recupération des valeurs retournées dans la promesse (ex: false ou erreurs à afficher si les champs requis pas bons)
                        // console.log(JSON.stringify(answer));
                        // si une chatroom a été enregistré
                        if (answer === true) {
                            // on vide le tableau d'erreurs
                            self.errorsArray = [];
                            // on met à jour la liste des chatrooms de l'utilisateur
                            self.getChatroomUser(e);
                        }
                        else {
                            // sinon on affiche les erreurs lui expliquant pourquoi il n'a pas pu créer une chatroom
                            self.errorsArray = answer;
                        }
                    });
                e.preventDefault();
            },
            getChatroomUser : function (e) {
                let self = this;
                let data = {
                    user_id: this.user.id
                };

                // appel fetch
                fetch('http://localhost/b3-chatroom-backend/controllers/chatrooms_controller.php?action=list', {
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
                    .then(function(answer) {
                        // recupération des valeurs retournées dans la promesse (ex: false ou chatroom(s) de l'utilisateur)
                        // console.log(JSON.stringify(answer));

                        if (answer.length !== 0) {
                            // stockage des différents chatrooms de l'user
                            self.chatroomDatasArray = answer;
                        }

                    });
                e.preventDefault();
            },
            getMessageUser : function (e) {
                let self = this;
                let data = {
                    user_id: this.user.id
                };

                // appel fetch
                fetch('http://localhost/b3-chatroom-backend/controllers/messages_controller.php?action=list', {
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
                    .then(function(answer) {
                        // recupération des valeurs retournées dans la promesse (ex: false ou chatroom(s) de l'utilisateur)
                        // console.log(JSON.stringify(answer));

                        if (answer.length !== 0) {
                            // stockage des différents chatrooms de l'user
                            self.messageDatasArray = answer;
                        }

                    });
                e.preventDefault();
            },
            /*getMessageChatroom : function (e) {
                let self = this;
                let data = {
                    user_id: this.user.id
                    chatroom_id: this.
                };

                // appel fetch
                fetch('http://localhost/b3-chatroom-backend/controllers/messages_controller.php?action=list', {
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
                    .then(function(answer) {
                        // recupération des valeurs retournées dans la promesse (ex: false ou chatroom(s) de l'utilisateur)
                        console.log(JSON.stringify(answer));

                        if (answer.length !== 0) {
                            // stockage des différents chatrooms de l'user
                            self.messageDatasArray = answer;
                        }

                    });
                e.preventDefault();
            }*/
        }
    }
</script>

<style scoped>
    form {
        width: 400px;
    }
</style>