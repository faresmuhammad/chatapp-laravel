<template>
    <div class="h-96 w-full">
        <div class="h-full p-2 flex flex-col-reverse overflow-scroll">
            <div v-for="(message, index) in props.messages" :key="index">
                <message-item :message="message"/>
            </div>
        </div>
    </div>
</template>

<script setup>
import MessageItem from "@/Pages/Chat/MessageItem.vue";

const props = defineProps(['messages'])
console.log("container messages: ")
console.log(props.messages)
</script>
