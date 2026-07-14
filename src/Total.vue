<script setup>
import { useVisuallyJsUpdate } from "@visuallyjs/browser-ui-vue"
import { ref } from "vue";

const total = ref(0)

useVisuallyJsUpdate((model) => {
    const nodeTotal = model.getNodes().map(n => n.data).reduce((acc, current) => acc + (current.monthlyPrice || 0), 0)
    const groupTotal = model.getGroups().map(n => n.data).reduce((acc, current) => acc + (current.monthlyPrice || 0), 0)
    total.value = nodeTotal + groupTotal
})
</script>

<template>
    <div style="position:absolute; top:0.5rem; right:0.5rem; font-size:27px">${{ total.toFixed(2) }}</div>
</template>
