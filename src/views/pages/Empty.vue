<script setup>
import { ref } from "vue";

const date = ref(null);
const dropdownValue = ref(null);
const dropdownValues = ref([
    { name: 'Tidak Tercapai' },
    { name: 'Tercapai' },
]);

const nestedRouteItems = ref([
    {
        label: "Al-Qur'an",
        to: "/pages/menu/alquran"
    },
    {
        label: "Tahlil dan Wirid",
        to: "/pages/menu/tahlilwirid"
    },
    {
        label: "Sholat dan Wudhu",
        to: "/pages/menu/sholatwudhu"
    },
]);
</script>
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
                        <Button class="w-2" label="Cek"></Button>
                    </div>
                </div>
            </div>
        </div>

        <div class="col-12 md:col-7">
            <div class="card p-fluid">
                <h3>Santri</h3>
                <div class="formgrid grid">
                    <div class="field col-7">
                        <label for="name2">Nama</label>
                        <InputText id="name2" type="text" value="Nama Santri" disabled />
                    </div>
                    <div class="field col-5">
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
                <div class="col-12 md:col-6">
                    <div class="card card-w-title">
                        <TabMenu :model="nestedRouteItems" />
                        <router-view />
                    </div>
                </div>
                <div class="flex flex-row-reverse">
                    <Button label="Simpan" icon="pi pi-save" class="w-fit mr-2"></Button>
                </div>
            </div>
        </div>
    </div>
</template>