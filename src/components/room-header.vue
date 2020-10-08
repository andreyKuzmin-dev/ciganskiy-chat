<template><div class="chat-box-header chat-box-flex-item">
    <div class="room-header-title">
        <h2><slot></slot></h2>
        <div class="online-indicator" :style="`background: ${is_online ? '#50b550' : '#b53f38'}`"></div>
    </div>
    <p class="room-header-exit pointer push" @click="backToRooms">Выйти</p>
</div></template>

<script>
    export default {
        props: {
            is_online: Boolean,
        },
        name: "room-header",
        methods: {
            backToRooms() {
                this.$emit('roomClosed');
            }
        }
    }
</script>

<style scoped>
    .room-header-title {
        display: flex;
        position: absolute;
        bottom: 10px;
        left: 40px;
        align-items: center;
        font-size: 20px;
    }
    .online-indicator {
        width: 12px;
        height: 12px;
        margin-left: 10px;
        border-radius: 7px;
        transition-property: background;
        transition-duration: 400ms;
    }
    .room-header-exit {
        position: absolute;
        bottom: 10px;
        right: -20%;
    }
</style>