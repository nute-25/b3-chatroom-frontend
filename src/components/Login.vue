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
            <h2>Connected user : {{ user.handle }} </h2>

            <!--Modification d'une chatroom-->
            <h3>Update a user</h3>
            <form @submit="updateUser">
                <fieldset>
                    <legend>Update user</legend>
                    <label for="userLogin">login</label>
                    <input type="text" id="userLogin" name="login" v-model="newLoginUser">
                    <label for="userPassword">password</label>
                    <input type="password" id="userPassword" name="password" v-model="newPasswordUser">
                    <label for="userHandle">handle</label>
                    <input type="text" id="userHandle" name="handle" v-model="newHandleUser">
                </fieldset>
                <input type="submit" value="Update">
            </form>

            <!--CHATROOM-->

            <hr>
            <!--Création Chatroom par un utilisateur-->
            <h3>Create a chatroom</h3>
            <form @submit="createChatroom">
                <fieldset>
                    <legend>Chatroom register</legend>
                    <label for="chatroomTitle">title</label>
                    <input type="text"  name="title" id="chatroomTitle" v-model="newChatroom" placeholder="Enter chatroom's title">
                </fieldset>
                <input type="submit" value="Create">
            </form>

            <hr>
            <!--Affichage Chatroom de l'utilisateur-->
            <h3>Chatrooms list of user</h3>
            <table>
                <thead>
                <tr>
                    <th>id</th>
                    <th>title</th>
                    <th>created</th>
                    <th>modified</th>
                    <th>update</th>
                    <th>messages</th>
                </tr>
                </thead>
                <tbody>
                <tr v-if="chatroomsUser.length === 0">
                    <td colspan="5">No chatroom found</td>
                </tr>
                <tr v-else v-for="chatroomData in chatroomsUser" v-bind:key="chatroomData.title">
                    <td>{{ chatroomData.id }}</td>
                    <td>{{ chatroomData.title }}</td>
                    <td>{{ chatroomData.created }}</td>
                    <td>{{ chatroomData.modified }}</td>
                    <td><button v-on:click="displayUpdateChatroomForm(chatroomData.title)">update</button></td>
                    <td><button v-on:click="displayMessages(chatroomData.id, chatroomData.title)">display</button></td>
                </tr>
                </tbody>
            </table>
            <hr>
            <!--Modification d'une chatroom-->
            <h3>Update a chatroom </h3>
            <form @submit="updateChatroom">
                <fieldset>
                    <legend>Update chatroom</legend>
                    <label for="newTitleChat">title</label>
                    <input type="text"  name="title" id="newTitleChat" placeholder="Enter chatroom's title" v-model="newTitleChat">
                </fieldset>
                <input type="submit" value="Update">
            </form>

            <div v-if="messages === true">
                <hr>
                <!--Affichage message d'une chatroom-->
                <h3>Message list of {{ selectedChatroom }}</h3>
                <table>
                    <thead>
                    <tr>
                        <th>user</th>
                        <th>content</th>
                        <th>created</th>
                    </tr>
                    </thead>
                    <tbody>
                    <tr v-if="msgchatDatasArray.length === 0">
                        <td colspan="5">No message found</td>
                    </tr>
                    <tr v-else v-for="msgchatData in msgchatDatasArray" v-bind:key="msgchatData.content">
                        <td>{{ msgchatData.user_id }}</td>
                        <td>{{ msgchatData.content }}</td>
                        <td>{{ msgchatData.created }}</td>
                    </tr>
                    </tbody>
                </table>


                <!--MESSAGES-->

                <hr>
                <!--Un utilisateur peut poster un message dans une chatroom-->
                <h3>Add message in Chatroom</h3>
                <form @submit="sendMessage">
                    <fieldset>
                        <legend>Send message in Chatroom</legend>
                        <label for="messageContent">Content</label>
                        <textarea name="messageContent" id="messageContent" v-model="messageContent" placeholder="Enter your message"></textarea>
                        <label for="messagechatroom">Chatroom</label>
                        <select name="messagechatroom" id="messagechatroom" v-model="messageChatroom">
                            <option v-for="chatroomData in chatroomsUser" v-bind:key="chatroomData.title" :value="chatroomData.id">{{ chatroomData.title }}</option>
                        </select>
                    </fieldset>
                    <input type="submit" value="Send Message">
                </form>
            </div>

            <hr>
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
                    <td><button v-on:click="deleteMessage(messageData.id)">delete</button></td>
                </tr>
                </tbody>
            </table>


        </div>


    </div>
</template>

<script>
    export default {
        name: "Login",
        data() {
            return {
                user: false,
                messages: false,

                userLogin: '',
                userPassword: '',

                //modification des info utilisateur
                updateLoginUser: '',
                updatePasswordUser: '',
                updateHandleUser: '',
                newLoginUser: '',
                newPasswordUser: '',
                newHandleUser: '',

                messageDatasArray: [],

                // formulaire ajout d'une chatroom
                newChatroom: '',
                // liste des chatrooms utilisateur
                chatroomsUser: [],

                // formulaire ajout message à une chatroom
                messageContent: '',
                messageChatroom: '',

                selectedChatroom: '',
                selectedChatroomId: '',
                msgchatDatasArray: [],

                // titre de la chatroom sélectionner et à modifier
                updateTitleChat: '',
                // titre donné à la chatroom dans le form de modification
                newTitleChat: '',

                errorsArray: [],


            }
        },
        methods : {
            updateUser: function (e) {
                let self = this;
                let data = {
                    user_id: this.user.id,
                    last_login: this.updateLoginUser,
                    last_password: this.updatePasswordUser,
                    last_handle: this.updateHandleUser,
                    login: this.newLoginUser,
                    password: this.newPasswordUser,
                    handle: this.newHandleUser
                };

                // appel fetch
                fetch('http://localhost/b3-chatroom-backend/controllers/users_controller.php?action=update', {
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
                            self.user.handle = this.newHandleUser;
                            // self.updateHandleUser = this.newHandleUser;
                        }
                        else {
                            // sinon on affiche les erreurs lui expliquant pourquoi il n'a pas pu créer une chatroom
                            self.errorsArray = answer;
                        }
                    });
                e.preventDefault();
            },
            sendLogin: function (e) {
                let self = this;
                let data = {
                    login: this.userLogin,
                    password: this.userPassword
                };

                // appel fetch
                fetch('http://localhost/b3-chatroom-backend/controllers/users_controller.php?action=login', {
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
                        // recupération des valeurs retournées dans la promesse (ex: false ou erreurs à afficher si login pas bon)
                        /*console.log(JSON.stringify(answer));
                        console.log(answer['id']);*/
                        // si un user s'est connecté
                        if (answer.login) {
                            // on enregistre ses données utilisateur
                            self.user = answer;
                            // on vide le tableau d'erreurs
                            self.errorsArray = [];
                            self.getMessageUser();
                            self.getChatroomUser(e);

                            self.updateLoginUser = answer.login;
                            self.updatePasswordUser = answer.password;
                            self.updateHandleUser = answer.handle;
                            self.newLoginUser = answer.login;
                            self.newPasswordUser = answer.password;
                            self.newHandleUser = answer.handle;
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
                        // si une chatroom a été enregistré
                        if (answer === true) {
                            // on vide le tableau d'erreurs
                            self.errorsArray = [];

                            // on vide les champs du formulaire
                            self.messageContent = '';
                            self.messageChatroom = '';

                            // on met à jour les messages de l'utilisateur
                            self.getMessageUser();
                            self.displayMessages(self.selectedChatroomId,self.selectedChatroom);
                        }
                        else {
                            // sinon on affiche les erreurs lui expliquant pourquoi il n'a pas pu créer une chatroom
                            self.errorsArray = answer;
                        }
                    });
                e.preventDefault();
            },
            getMessageUser : function () {
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

                        if (answer.length !== 0) {
                            // stockage des différents chatrooms de l'user
                            self.messageDatasArray = answer;
                        }

                    });
            },
            deleteMessage : function(msg_id) {
                let self = this;

                // appel fetch
                fetch('http://localhost/b3-chatroom-backend/controllers/messages_controller.php?action=delete', {
                    method: 'POST', // or 'PUT'
                    body: JSON.stringify(msg_id), // data can be string or {object}!
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
                        // si un message a été supprimé
                        if (answer === true) {
                            // on vide le tableau d'erreurs
                            self.errorsArray = [];
                            // on recharche la liste des messages utilisateur
                            self.getMessageUser();
                        }
                    });
            },
            createChatroom : function(e) {
                let self = this;
                let data = {
                    title: this.newChatroom,
                    user_id: this.user.id
                };

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
                        // si une chatroom a été enregistré
                        if (answer === true) {
                            // on vide le tableau d'erreurs
                            self.errorsArray = [];

                            // on vide le champ du formulaire
                            self.newChatroom = '';

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

                        if (answer.length !== 0) {
                            // stockage des différents chatrooms de l'user
                            self.chatroomsUser = answer;
                        }

                    });
                e.preventDefault();
            },
            displayUpdateChatroomForm: function (chat_title) {
                this.updateTitleChat = chat_title;
                this.newTitleChat = chat_title;
            },
            updateChatroom: function (e) {
               let self = this;
               let data = {
                   last_title: this.updateTitleChat,
                   title: this.newTitleChat
               };

                // appel fetch
                fetch('http://localhost/b3-chatroom-backend/controllers/chatrooms_controller.php?action=update', {
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
            displayMessages: function (chat_id, chat_title) {
                let self = this;
                let data = {
                    chatroom_id: chat_id
                };

                // appel fetch
                fetch('http://localhost/b3-chatroom-backend/controllers/chatrooms_controller.php?action=displayMessages', {
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

                        if (answer.length !== 0) {
                            // stockage des différents messages des chatrooms
                            self.msgchatDatasArray = answer;
                            self.selectedChatroom = chat_title;
                            self.selectedChatroomId = chat_id;

                            self.messages = true;
                        }

                    });
            }
        }
    }
</script>

<style scoped>
    form {
        width: 400px;
    }
</style>