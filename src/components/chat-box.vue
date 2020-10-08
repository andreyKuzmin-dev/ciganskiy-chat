<template><div class="chat-box chat-box-flex-container">
    <slot name="header"></slot>
    <slot name="body"></slot>
    <slot name="footer"></slot>
</div></template>

<script>
    export default {
        name: "chat-box"
    }
</script>

<style>
    .chat-box-flex-container {
        display: flex;
        flex-direction: column;
        flex-wrap: nowrap;
        justify-content: flex-start;
        align-content: stretch;
        align-items: flex-start;
    }
    .chat-box-flex-item:nth-child(1) {
        order: 0;
        flex: 0 1 auto;
        align-self: auto;
    }
    .chat-box-flex-item:nth-child(2) {
        order: 0;
        flex: 1 1 auto;
        align-self: auto;
    }
    .chat-box-flex-item:nth-child(3) {
        order: 0;
        flex: 0 1 auto;
        align-self: auto;
    }
    .chat-box {
        min-height: 100vh;
    }
    .chat-box-header {
        position: relative;
        width: 80%;
        min-height: 80px;
        border-bottom: 2px #2B2B2B solid;
    }
    .chat-box-body {
        display: flex;
        width: 100%;
    }
    .chat-box-footer {
        width: 100%;
        min-height: 150px;
        border-top: 2px #2B2B2B solid;
    }
</style>