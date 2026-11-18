<script setup>
defineProps({
    tecles: Array,
    bloquejat: Boolean,
    premudes: Array
})

const emit = defineEmits(['premuda'])
</script>

<template>
    <div class="keypad">

        <button
            v-for="tecla in tecles"
            :key="tecla"
            :disabled="bloquejat || premudes.includes(tecla)"
            @click="emit('premuda', tecla)"
        >
            {{ tecla }}
        </button>

    </div>
</template>