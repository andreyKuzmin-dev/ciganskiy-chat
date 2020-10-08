<template><div class="chat-box-body chat-box-flex-item">
    <div class="content">
        <div class="messages-container">
            <div class="messages">
                <div class="message" v-for="(message, i) in messages">
                    <div class="username-container">
                        <p class="message-username">
                            {{message.username}}
                        </p>
                    </div>
                    <div class="message-container">
                        <p class="message-text">
                            {{message.text}}
                        </p>
                    </div>
                </div>
                <div id="bottom"></div>
            </div>
        </div>
    </div>

    <div class="members-container">
        <div class="members">
            <p class="member" v-for="(member, name) in online_members" v-if="member.isOnline">
                {{name}}
            </p>
        </div>
    </div>
</div></template>

<script>
    export default {
        props: {
            room: String,
            messages: Array,
            online_members: Object,
        },
        name: "room-content"
    }
</script>

<style scoped>
    .content {
        position: relative;
        width: 75%;
    }
    .messages-container {
        height: 93%;
        width: 96%;
        margin: 15px 0 2% 2%;
        overflow: hidden;
    }
    .messages {
        height: 71vh;
        width: 95%;
        margin: 0 0 2% 2%;
        padding-right: 20px;
        overflow-y: scroll;
        overflow-x: hidden;
    }
    .members-container {
        width: 22%;
        border-left: 2px #2B2B2B solid;
        overflow: hidden;
    }
    .members {
        width: 90%;
        height: 93%;
        margin: 15px 0 10% 10%;
        overflow-y: scroll;
        overflow-x: hidden;
    }
    .member {
        margin: 15px 0;
        text-align: left;
    }
    .message {
        display: flex;
        margin: 15px 0;
        padding-bottom: 14px;
        border-bottom: 2px solid #595959;
        flex-direction: column;
        text-align: left;
    }
    .message-username {
        min-width: 150px;
        max-width: 150px;
        padding: 5px;
        text-align: left;
        word-break: break-word;
        background-color: #3a3a3a;
    }
    .message-text {
        margin-left: 168px;
        margin-top: -22px;
        text-align: left;
    }
</style>