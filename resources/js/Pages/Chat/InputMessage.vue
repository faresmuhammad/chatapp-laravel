<script setup>
import {ref} from "vue";
import axios from "axios";

const props = defineProps(['room'])
const emits = defineEmits(['messagesent'])
let message = ref('')

const sendMessage = () => {
    console.log(message.value)
    if (message.value === ' ') return
    axios.post('/chat/room/' + props.room.id + '/new-message', {
        message: message.value
    })
        .then(function (response)  {
            console.log("send message status: " + response.status)
            if (response.status === 201) {
                message.value = ''
                emits('messagesent')
            }
        })
        .catch(error => {
            console.log(error)
        })
}
</script>
<template>
    <div class="relative h-10 m-1">
        <div style="border-top: 1px solid #e6e6e6;" class="grid grid-cols-6">
            <input
                type="text"
                v-model="message"
                @keyup.enter="sendMessage()"
                placeholder="Say something..."
                class="col-span-5 outline-none p-1"
            />
            <button
                @click="sendMessage()"
                class="place-self-end bg-gray-500 hover:bg-blue-700 p-1 mt-1 rounded text-white">
                Send
            </button>
        </div>
    </div>
</template>

