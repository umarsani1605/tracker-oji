<template>
    <div class="grid bg-black">
        <div class="col-12 md:col-5">
            <div class="card p-fluid">
                <h3>Pentashih</h3>
                <div class="field">
                    <label for="name1">Nama</label>
                    <InputText id="name1" type="text" />
                    <!-- <small>Enter your username to reset your password.</small> -->
                </div>
                <div class="field">
                    <label for="email1">Kode Santri</label>
                    <div class="flex flex-row gap-3">
                        <InputText id="email1" type="text" />
                        <Button class="w-4 md:w-3" label="Cek"></Button>
                    </div>
                </div>
            </div>
        </div>

        <div class="col-12 md:col-7">
            <div class="card p-fluid">
                <h3>Santri</h3>
                <div class="formgrid mb-5 md:grid">
                    <div class="field md:col-7">
                        <label for="name2">Nama</label>
                        <InputText id="name2" type="text" value="Nama Santri" disabled />
                    </div>
                    <div class="field md:col-5">
                        <label for="email2">Tanggal</label>
                        <InputGroup>
                            <Calendar inputId="calendar" v-model="date"></Calendar>
                            <InputGroupAddon>
                                <i class="pi pi-calendar"></i>
                            </InputGroupAddon>
                        </InputGroup>
                    </div>
                </div>
                <Divider class="my-3" />
                <div class="">
                    <TabView :scrollable="true">
                        <TabPanel v-for="(tab, index) in tabItems" :key="index" :header="tab.title">
                            <div v-for="(value, index) in tab.children" :key="index" class="py-3">
                                <h5>{{ value.title }}</h5>
                                <div class="formgrid md:grid">
                                    <div v-if="value.setoran" class="field md:col-6">
                                        <label for="name2">Setoran</label>
                                        <Dropdown v-model="dropdownValue" :options="dropdownValues" optionLabel="name" placeholder="Capaian" />
                                    </div>
                                    <div v-if="value.hafalan"class="field md:col-5">
                                        <label for="email2">Hafalan</label>
                                        <Dropdown v-model="dropdownValue" :options="dropdownValues" optionLabel="name" placeholder="Capaian" />
                                    </div>
                                </div>
                            </div>
                        </TabPanel>
                    </TabView>
                </div>
                <div class="flex flex-row-reverse">
                    <Button label="Simpan" icon="pi pi-save" class="w-fit mr-2"></Button>
                </div>
            </div>
        </div>
    </div>
</template>

<style>
.p-tabview-panels {
    padding: 1rem 0.25rem;
}
</style>

<script setup>
import { ref, watch } from 'vue';

const date = ref(null);

const dropdownValue = ref(null);
const dropdownValues = ref([
    { name: 'Tidak Tercapai' },
    { name: 'Tercapai' },
]);

const tabItems = [
    {
        title: "Al-Qur'an",
        children: [
            {
                title: "Juz Amma",
                hafalan: true,
                setoran: true,
            },
            {
                title: "Al-Mulk",
                hafalan: true,
                setoran: true,
            },
            {
                title: "As-Sajdah",
                hafalan: true,
                setoran: true,
            },
            {
                title: "As-Waqiah",
                hafalan: true,
                setoran: true,
            },
            {
                title: "As-Rahman",
                hafalan: true,
                setoran: true,
            },
            {
                title: "Yasin",
                hafalan: true,
                setoran: true,
            },
        ]
    },
    {
        title: "Tahlil dan Wirid",
        children: [
            {
                title: "Tahlil",
                hafalan: true,
                setoran: true,
            },
            {
                title: "Wirid",
                hafalan: true,
                setoran: true,
            },
            {
                title: "Istighosah",
                hafalan: true,
                setoran: true,
            },
            {
                title: "Ratibul Haddad",
                hafalan: false,
                setoran: true,
            },
            {
                title: "Maulid Barzanji",
                hafalan: false,
                setoran: true,
            }
        ]
    },
    {
        title: "Sholat dan Wudhu",
        children: [
            {
                title: "Bacaan Sholat",
                hafalan: true,
                setoran: true,
            },
            {
                title: "Bacaan Wudhu",
                hafalan: true,
                setoran: true,
            },
        ]
    },
];

</script>