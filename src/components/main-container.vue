<template><div class="main-container">
    <div class="layout">
        <chat-box>
            <template v-slot:header>
                <room-header @roomClosed="closeRoom"
                             v-if="views_state['room-header']" :is_online="statuses.isConnected">
                    {{room}}
                </room-header>
            </template>
            <template v-slot:body>
                <login-selector @loginSelected="setLogin"
                                v-if="views_state['login-selector']"></login-selector>
                <room-selector @roomSelected="setRoom"
                               v-if="views_state['room-selector']"></room-selector>
                <room-content v-if="views_state['room-content']"
                              :room="room ? room : ''"
                              :online_members="online_members"
                              :messages="messages ? messages : []"
                              ref="content"></room-content>
            </template>
            <template v-slot:footer>
                <message-sender @messageSend="sendMessage" v-if="views_state['message-sender']"></message-sender>
            </template>
        </chat-box>
    </div>
</div></template>

<script>
    import ChatBox from "./chat-box"
    import RoomHeader from "./room-header";
    import MessageSender from "./message-sender";
    import LoginSelector from "./login-selector";
    import RoomContent from "./room-content";
    import RoomSelector from "./room-selector";
    export default {
        name: "main-container",
        components: {RoomSelector, RoomContent, LoginSelector, MessageSender, RoomHeader, ChatBox},
        data() {
            return {
                views_state: {
                    'room-header': false,
                    'message-sender': false,
                    'login-selector': true,
                    'room-selector': false,
                    'room-content': false,
                },

                username: '',
                room: '',
                ws: false,
                messages: [],
                rooms: [],
                online_members: {
                        stranger: {isOnline: true, timeout: false}
                    },

                statuses: {
                    isConnected: false,
                    isReconnecting: false,
                },

                createRoomMessages: [
                    'Я создатель, му-ах-хаха! СОЗДАТЕЛЬ!',
                    'Добро пожаловать в мою комнату!',
                    'Это я сделаль',
                    'Так-ссс... ну, немного прибраться и будет в самый раз!',
                    'Ну, пока никого нету, можно и подремать!',
                    'Правила комнаты простые, правда я их забыл, но ты не нарушай!',
                    'Какая хорошая комната, кто же её создал? Ах, да! Это же я!',
                    'Вот какая у меня теперь классная комната!',
                ]
            }
        },
        methods: {
            setLogin({login}) {
                this.username = login;
                this.online_members = {};
                this.online_members[login] = {isOnline: true, timeout: false};
                this.views_state["login-selector"] = false;
                this.views_state["room-selector"] = true;
                this._connect();
            },
            setRoom({room, create}) {
                this.room = room;
                if (create) {
                    const text = this._getRandomMessageText('create');
                    if (text) {
                        this.sendMessage({text})
                    }
                } else {
                    this._getHistory();
                }
                this.views_state["room-selector"] = false;
                this.views_state["room-header"] = true;
                this.views_state["room-content"] = true;
                this.views_state["message-sender"] = true;
            },
            _getRandomMessageText(event) {
                switch (event) {
                    case 'create':
                        const messages = this.createRoomMessages;
                        const index = Math.round((Math.random()*messages.length));
                        return messages[index];
                        break;
                    default:
                    return false;
                }
            },
            closeRoom() {
                this.room = '';
                this.online_members = {};
                this.online_members[this.username] = {isOnline: true, timeout: false};
                this.messages = [];
                this.views_state["room-content"] = false;
                this.views_state["message-sender"] = false;
                this.views_state["room-header"] = false;
                this.views_state["room-selector"] = true;
            },
            async sendMessage({text}) {
                const msg = {
                    room: this.room,
                    text,
                };
                this.ws.send(JSON.stringify(msg));
            },
            async _getHistory() {
                fetch(`https://nane.tada.team/api/rooms/${this.room}/history`)
                    .then(response => response.json())
                    .then(({result}) => {
                        this.messages = [];
                        if (Array.isArray(result)) {
                            result.map(el=>{
                                if (el.sender && el.sender.username && el.text) {
                                    this.messages.push({
                                        username: el.sender.username,
                                        text: el.text,
                                    })
                                }
                            });
                            this._scrollChatToBottom()
                        }
                    })
            },
            _connect() {
                let url = 'wss://nane.tada.team/ws?username=' + this.username;
                this.ws = new WebSocket(url);
                this.ws.addEventListener("open", this._open.bind(this));
                this.ws.addEventListener("close", this._close.bind(this));
                this.ws.addEventListener("message", this._message.bind(this));
            },
            _open() {
                this.statuses.isConnected = true;
            },
            _close() {
                this.statuses.isConnected = false;
                this.statuses.isReconnecting = true;
                setTimeout(this._connect, 3000);
            },
            _message(e) {
                let msg;
                try {
                    msg = JSON.parse(e.data);
                } catch (e) {
                    return false;
                }
                if(!msg.sender || !msg.text || !msg.room || msg.room !== this.room) return;
                const username = msg.sender.username;
                const text = msg.text;
                this.messages.push({
                    username,
                    text,
                });
                this._scrollChatToBottom();
                if (username !== this.username) this._setMemberOnline(username);
            },
            _setMemberOnline(username) {
                if (this.online_members[username]) {
                    clearTimeout(this.online_members[username].timeout)
                }
                this.online_members[username] = {
                    isOnline: true,
                    timeout: false
                };
                this.online_members[username].timeout = setTimeout(function () {
                    this.online_members[username].isOnline = false;
                    if (this.$refs.content) {
                        this.$refs.content.$forceUpdate()
                    }
                }.bind(this),300000)  // 5 minutes
            },
            _scrollChatToBottom() {
                const messages = document.getElementById('messages');
                if (!messages) return;
                setTimeout(()=>{messages.scrollTop = messages.scrollHeight},1);
            }
        }
    }
</script>

<style scoped>
    .layout {
        width: 1200px;
        margin: 0 auto;
        transition-property: width;
        transition-duration: 300ms;
        background-image: url(/background.svg);
    }
    .main-container {
        min-height: 100vh;
    }
    @media (max-width: 1200px)  {
        .layout {
            width: 80vw;
            min-width: 400px;
            transition-duration: 100ms;
        }
    }
</style>