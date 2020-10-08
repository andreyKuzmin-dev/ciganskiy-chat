<template><div class="chat-box-footer chat-box-flex-item">
    <div class="new-message">
        <input class="new-message-input" type="text" v-model="new_message_text"/>
        <p class="new-message-button pointer push" @click="sendMessage">Отправить</p>
        <p class="new-message-error" v-show="error">{{error}}</p>
    </div>
</div></template>

<script>
    export default {
        name: "message-sender",
        data() {
            return {
                new_message_text: '',
                error: false,
            }
        },
        methods: {
            sendMessage() {
                if (!this._isMessageFine()) return;
                this.error = false;
                this.$emit('messageSend', {text: this.new_message_text});
                this.new_message_text = '';
            },
            _isMessageFine(message = this.new_message_text) {
                let min = 1;
                let max = 300;
                let error;
                if (error = this._isLengthNotInRange(message.length, [min, max])) {
                    this.error = error;
                    return false;
                } else if (error = this._isCharsNotAvailable(message)) {
                    this.error = error;
                    return false;
                }
                return true;
            },
            _isLengthNotInRange(length, range) {
                if (length < range[0] || length > range[1]) {
                    return `В связи с корона-вирусом, сообщение должно содержать от ${range[0]} до ${range[1]} символов!`;
                }
            },
            _isCharsNotAvailable(message) {
                const availableChars = 'abcdefghijklmnopqrstuvwxyzабвгдеёжзийклмнопрстуфхцчшщъыьэюя0123456789_@-—,№#$&.!?%^*"\':><()';
                let noChar = true;
                for (const char of message) {
                    if (!availableChars.includes(char.toLowerCase()) && char !== ' ') {
                        return `Один уважаемый человек очень просил не использовать "${char}", замени, пожалуйста, на что-нибудь из алфавита или цифр!`;
                    }
                    if (char !== ' ') {
                        noChar = false;
                    }
                }
                if (noChar) {
                    return 'Помолчать можно и не отправляя сообщения :)';
                }
                return false;
            }
        }
    }
</script>

<style scoped>
    .new-message {
        position: relative;
        display: flex;
        width: 90%;
        height: 90%;
        margin: 20px 0 0 19%;
    }
    .new-message-input {
        color: white;
        font-size: 16px;
        width: 50%;
        height: 30px;
        outline: none;
        border: none;
        border-bottom: 1px gray solid;
        background: none;
    }
    .new-message-error {
        position: absolute;
        top: 50px;
        left: 0;
        width: 100%;
        color: #ffa0a0;
        text-align: left;
    }
    .new-message-button {
        line-height: 30px;
        margin-left: 25px;
    }
</style>