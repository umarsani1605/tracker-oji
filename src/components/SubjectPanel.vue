<template>
  <div class="py-3">
    <h5>{{ subject.title }}</h5>
    <div class="formgrid md:grid">
        <div v-if="subject.setoran" class="field md:col-6">
            <label for="name2">Setoran</label>
            <Dropdown v-model="dropdownValue" :options="dropdownValues" optionLabel="name" placeholder="Capaian" />
        </div>
        <div v-if="subject.hafalan"class="field md:col-5">
            <label for="email2">Hafalan</label>
            <Dropdown v-model="dropdownValue" :options="dropdownValues" optionLabel="name" placeholder="Capaian" />
        </div>
    </div>
  </div>
</template>
<script setup>
import { onMounted } from 'vue';

const subject = defineProps({})

onMounted(() => {
    console.log(subject.value);
})
</script>