
<script setup>
const props = defineProps(['message'])

</script>

<template>
    <div style="display: block">
        {{props.message.user.name}}: {{props.message.message}}
    </div>
</template>
