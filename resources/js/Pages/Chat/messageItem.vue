<template>
    <div style="border-bottom: 1px solid whitesmoke" class="p-b-3">
        {{ message.user.name }} : {{ message.message }}
    </div>
</template>

<script>
export default {
    props: ["message"],
};
</script>
