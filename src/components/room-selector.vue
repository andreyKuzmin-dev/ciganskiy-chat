<template><div class="room-selector">
    <div class="room-create">
        <p class="room-create-info">Выбери одну из комнат в списке справа или создай свою собственную!</p>
        <input class="room-create-input" type="text" v-model="room" placeholder="Название новой комнаты"/>
        <p class="room-create-button push pointer" @click="createRoom">Создать</p>
        <p class="room-create-error" v-show="error">{{error}}</p>
    </div>
    <div class="rooms-container">
        <p class="room-name push pointer" v-for="room in rooms" @click="selectRoom(room)">
            {{room}}
        </p>
    </div>
</div></template>

<script>
    export default {
        name: "room-selector",
        data() {
            return {
                rooms: [
                ],
                room: '',
                error: false,
            }
        },
        mounted() {
            this.getRooms();
        },
        methods: {
            async getRooms() {
                fetch('https://nane.tada.team/api/rooms')
                    .then(response => response.json())
                    .then(({result}) => {
                        this.rooms = [];
                        if (Array.isArray(result)) {
                            result.map(el=>{
                                if (el.name) {
                                    this.rooms.push(el.name)
                                }
                            })
                        }
                    })
            },
            createRoom() {
                if (!this._isRoomFine()) return;
                this.error = false;
                this.$emit('roomSelected', {room: this.room, create: true});
                this.getRooms();
            },
            selectRoom(room) {
                this.$emit('roomSelected', {room, create: false});
            },
            _isRoomFine(room = this.room) {
                let min = 1;
                let max = 50;
                let error;
                if (error = this._isLengthNotInRange(room.length, [min, max])) {
                    this.error = error;
                    return false;
                } else if (error = this._isCharsNotAvailable(room)) {
                    this.error = error;
                    return false;
                }
                return true;
            },
            _isLengthNotInRange(length, range) {
                if (length < range[0] || length > range[1]) {
                    return `Опа! А название должно содержать от ${range[0]} до ${range[1]} символов!`;
                }
            },
            _isCharsNotAvailable(room) {
                const availableChars = 'abcdefghijklmnopqrstuvwxyzабвгдеёжзийклмнопрстуфхцчшщъыьэюя0123456789_@-—,№#$&.!?%^*"\':><()';
                let noChar = true;
                for (const char of room) {
                    if (!availableChars.includes(char.toLowerCase()) && char !== ' ') {
                        return `Блин! У меня нету "${char}", чтобы на дверь приколотить, давай чего-нибудь другое из алфавита или цифр!`;
                    }
                    if (char !== ' ') {
                        noChar = false;
                    }
                }
                if (noChar) {
                    return 'Не пущу! В названии пусто - в комнате пусто!';
                }
                return false;
            }
        }
    }
</script>

<style scoped>
    .room-selector {
        display: flex;
        position: relative;
        width: 100%;
        height: 100vh;
        flex-direction: row;
        justify-content: flex-end;
        align-items: center;
    }
    .room-create {
        position: relative;
        width: 25%;
        min-width: 200px;
        height: 400px;
        margin: 0 10% 0 10%;
        text-align: right;
    }
    .room-create-info {
        position: absolute;
        top: 0;
        left: 0;
    }
    .room-create-input {
        position: absolute;
        width: 100%;
        height: 30px;
        top: 80px;
        left: 0;
        padding: 0;
        color: white;
        font-size: 16px;
        text-align: right;
        outline: none;
        border: none;
        border-bottom: 1px gray solid;
        background: none;
    }
    .room-create-button {
        position: absolute;
        width: 100%;
        line-height: 30px;
        top: 160px;
        left: 0;
        transition-duration: 300ms;
    }
    .room-create-button:hover {
        padding-right: 10px;
        background-color: #fafafa;
        color: grey;
        border-top-left-radius: 30px;
        border-bottom-left-radius: 30px;
    }
    .room-create-error {
        position: absolute;
        width: 100%;
        top: 230px;
        left: 0;
        color: #ffa0a0;
        text-align: left;
    }
    .rooms-container {
        display: flex;
        position: relative;
        min-width: 134px;
        width: 40%;
        height: 87%;
        right: 0;
        padding-top: 10%;
        flex-direction: column;
        align-items: center;
        overflow-y: scroll;
        overflow-x: hidden;
        background-color: rgba(0,0,0,0.1);
    }
    .room-name {
        width: 100%;
        min-height: 25px;
        margin-top: 10px;
        transition-duration: 300ms;
    }
    .room-name:hover {
        transform: scale(1.2);
    }

</style>