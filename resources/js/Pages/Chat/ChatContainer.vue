<template>
    <AppLayout :title=" ' Room'">
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                <chat-room-selection
                    v-if="currentRoom != null"
                    :rooms="rooms"
                    :currentRoom="currentRoom"
                    v-on:roomchanged="setRoom($event)"
                />
            </h2>
        </template>

        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div class="bg-white overflow-hidden shadow-xl sm:rounded-lg">
                    <message-container :messages="messages"/>
                    <input-message
                        :room="currentRoom"
                        v-on:messagesent="getMessages()"
                    />
                </div>
            </div>
        </div>
    </AppLayout>
</template>

<script setup>
import AppLayout from '@/Layouts/AppLayout.vue';
import MessageContainer from "@/Pages/Chat/MessageContainer.vue";
import InputMessage from "@/Pages/Chat/InputMessage.vue";
import axios from "axios";
import {onMounted, onUpdated, reactive, ref, watch} from "vue";
import ChatRoomSelection from "@/Pages/Chat/ChatRoomSelection.vue";

onMounted(() => {
    getRooms()
})
onUpdated(() => {
    getRooms()
})


let rooms = ref([])
let currentRoom = ref(null)
let messages = ref(null)

watch(currentRoom, async (previous, current) => {
    if (previous != null) {
        disconnect(previous)
    }
    connect()
})

const getRooms = () => {
    axios.get('/chat/rooms')
        .then((response) => {
            rooms.value = response.data
            // console.log(rooms)
            setRoom(rooms.value[0])
            // getMessages()
        })
        .catch(error => {
            console.log(error)
        })
}

getRooms()
const getMessages = () => {
    console.log("messages room: " + currentRoom.value.id)
    axios.get('/chat/room/' + currentRoom.value.id + '/messages')
        .then(response => {
            messages.value = response.data
            console.log("current messages: ")
            console.log(messages.value)
        })
        .catch(error => {
            console.log(error)
        })
}

function setRoom(room) {
    currentRoom.value = room
    // console.log(currentRoom)
}

function connect() {
    if (currentRoom != null) {
        getMessages()
        const channel = 'chat.' + currentRoom.value.id
        window.Echo.private(channel)
            .listen('.App\\Providers\\NewChatMessage', e => {
                getMessages()
            })
        console.log(channel)
    }
}

function disconnect(room) {
    window.Echo.leave('chat.' + room.id)
}

</script>
