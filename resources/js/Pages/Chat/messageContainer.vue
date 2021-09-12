<template>
    <div class="h-90 w-full">
        <div class="h-full p-4 flex flex-col-reverse overflow-scroll">
            <div v-for="(message, index) in messages" :key="index">
                <message-item :message="message" />
            </div>
        </div>
    </div>
</template>

<script>
import messageItem from "./messageItem.vue";
export default {
    components: { messageItem },
    props: ["messages"],
};
</script>
