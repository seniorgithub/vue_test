<script setup>
    defineProps(['collection'])
</script>
<template>
    <ul class="side-menu">
        <li class="with-arrow">SPECTACLES
            <ul class="side-sub-menu">
                <li class="arrow" @click="$emit('collection', { key: 'spectacles', value: 'men' })">MEN</li>
                <li class="arrow" @click="$emit('collection', { key: 'spectacles', value: 'women' })">WOMEN</li>
            </ul>
        </li>
        <li class="with-arrow">SUNGLASSES
            <ul class="side-sub-menu">
                <li class="arrow" @click="$emit('collection', { key: 'sunglasses', value: 'men' })">MEN</li>
                <li class="arrow" @click="$emit('collection', { key: 'sunglasses', value: 'women' })">WOMEN</li>
            </ul>
        </li>
        <li>HOME TRY ON</li>
        <li>PAIR FOR PAIR</li>
    </ul>
</template>
