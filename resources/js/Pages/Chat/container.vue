<template>
    <app-layout title="Dashboard">
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                <chat-room-toggle
                    v-if="currentRoom.id"
                    :rooms="chatRooms"
                    :currentRoom="currentRoom"
                    v-on:roomchanged="setCurrentRoom($event)"
                />
            </h2>
        </template>

        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div class="bg-white overflow-hidden shadow-xl sm:rounded-lg">
                    <message-container :messages="messages" />
                    <input-message
                        :room="currentRoom"
                        v-on:messagesent="getMessages()"
                    />
                </div>
            </div>
        </div>
    </app-layout>
</template>

<script>
import { defineComponent } from "vue";
import AppLayout from "@/Layouts/AppLayout.vue";
import MessageContainer from "./messageContainer.vue";
import InputMessage from "./inputMessage.vue";
import ChatRoomToggle from "./chatRoomToggle.vue";
import axios from "axios";

export default {
    components: {
        AppLayout,
        MessageContainer,
        InputMessage,
        ChatRoomToggle,
    },
    data: function () {
        return {
            chatRooms: [],
            currentRoom: [],
            messages: [],
        };
    },
    watch: {
        currentRoom(val, oldVal) {
            if (oldVal.id) {
                // if the user was subscribed to a channel
                this.disconnect(oldVal); // disconnect it from that channel
            }
            this.connect();
        },
    },
    methods: {
        connect() {
            if (this.currentRoom.id) {
                let vm = this;
                this.getMessages();
                window.Echo.private("chat." + this.currentRoom.id).listen(
                    ".message.new",
                    (e) => {
                        vm.getMessages();
                    }
                );
            }
        },
        disconnect(room) {
            window.Echo.leave("chat." + room.id);
        },
        getRooms() {
            axios
                .get("/chat/rooms")
                .then((response) => {
                    this.chatRooms = response.data;
                    this.setCurrentRoom(response.data[0]);
                })
                .catch((error) => {
                    console.log("response is : ", error);
                });
        },
        setCurrentRoom(room) {
            this.currentRoom = room;
        },
        getMessages() {
            axios
                .get("/chat/room/" + this.currentRoom.id + "/messages")
                .then((response) => {
                    this.messages = response.data;
                })
                .catch((error) => {
                    console.log(error);
                });
        },
    },
    created() {
        this.getRooms();
    },
};
</script>
