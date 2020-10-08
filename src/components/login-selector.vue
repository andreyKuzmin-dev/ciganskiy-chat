<template><div class="login-selector">
    <div class="login-container">
        <img class="login-logo" src="/logo.svg" alt="logo"/>
        <input class="login-input" type="text" v-model="login" placeholder="Как тебя зовут, дружище? :)"/>
        <p class="login-button push pointer" @click="selectLogin">Войти</p>
        <p class="login-error" v-show="error">{{error}}</p>
    </div>
</div></template>

<script>
    export default {
        name: "login-selector",
        data() {
            return {
                login: '',
                error: false,
            }
        },
        methods: {
            selectLogin() {
                if (!this._isLoginFine()) return;
                this.error = false;
                this.$emit('loginSelected', {login: this.login.trim()});
            },
            _isLoginFine(login = this.login) {
                let min = 1;
                let max = 30;
                let error;
                if (error = this._isLengthNotInRange(login.length, [min, max])) {
                    this.error = error;
                    return false;
                } else if (error = this._isCharsNotAvailable(login)) {
                    this.error = error;
                    return false;
                }
                return true;
            },
            _isLengthNotInRange(length, range) {
                if (length < range[0] || length > range[1]) {
                    return `Ой! Логин должен содержать от ${range[0]} до ${range[1]} символов!`;
                }
            },
            _isCharsNotAvailable(login) {
                const availableChars = 'abcdefghijklmnopqrstuvwxyzабвгдеёжзийклмнопрстуфхцчшщъыьэюя0123456789_@.-—';
                let noChar = true;
                for (const char of login) {
                    if (!availableChars.includes(char.toLowerCase()) && char !== ' ') {
                        return `Упс! У меня аллергия на "${char}", замени, пожалуйста, на что-нибудь из алфавита или цифр!`;
                    }
                    if (char !== ' ') {
                        noChar = false;
                    }
                }
                if (noChar) {
                    return 'Ну и как к тебе обращаться? Мистер Пробел? Мммм?';
                }
                return false;
            }
        }
    }
</script>

<style scoped>
    .login-selector {
        display: flex;
        position: relative;
        width: 100%;
        height: 100vh;
        flex-direction: column;
        justify-content: center;
        align-items: center;
    }
    .login-container {
        position: relative;
        width: 400px;
        height: 400px;
    }
    .login-logo {
        position: absolute;
        width: 80%;
        top: 0;
        left: 10%;
    }
    .login-input {
        position: absolute;
        width: 100%;
        height: 30px;
        top: 130px;
        left: 0;
        padding: 0;
        color: white;
        font-size: 16px;
        text-align: center;
        outline: none;
        border: none;
        border-bottom: 1px gray solid;
        background: none;
    }
    .login-button {
        position: absolute;
        width: 100%;
        top: 180px;
        left: 0;
        margin-top: 25px;
        text-align: center;
        line-height: 30px;
        transition-duration: 300ms;
    }
    .login-button:hover {
        background-color: #fafafa;
        color: grey;
        border-radius: 30px;
    }
    .login-error {
        position: absolute;
        width: 100%;
        top: 270px;
        color: #ffa0a0;
    }
</style>