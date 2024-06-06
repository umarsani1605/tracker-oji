<template>
    <div class="grid bg-black">
        <div class="col-12 md:col-5">
            <div class="card p-fluid">
                <h3>Raport Santri</h3>
                <div class="field">
                    <label for="name1">Nama</label>
                    <InputText id="name1" type="text" />
                    <!-- <small>Enter your username to reset your password.</small> -->
                </div>
                <div class="field">
                    <label for="email1">Kode</label>
                    <div class="flex flex-row gap-3">
                        <InputText id="email1" type="text" />
                        <Button class="w-4 md:w-3" label="Cek"></Button>
                    </div>
                </div>
            </div>
        </div>

        <div class="col-12 md:col-7">
            <div class="card p-fluid">
                <h3>Capaian</h3>
                <DataTable :value="dataSantri" rowGroupMode="subheader" groupRowsBy="category" sortMode="single" sortField="category" :sortOrder="1" scrollable scrollHeight="400px" showGridlines>
                    <Column field="category" header="Representative"></Column>
                    <Column field="name" header="Nama" style="min-width: 8rem"></Column>
                    <Column field="setoran" header="Setoran" style="min-width: 9rem">
                        <template #body="slotProps">
                            <Tag :value="slotProps.data.setoran? 'Tercapai' : 'Tidak Tercapai'" :severity="slotProps.data.setoran? 'success' : 'danger'" />
                        </template>
                    </Column>
                    <Column field="hafalan" header="Hafalan" style="min-width: 9rem">
                        <template #body="slotProps">
                            <Tag :value="slotProps.data.hafalan? 'Tercapai' : 'Tidak Tercapai'" :severity="slotProps.data.hafalan? 'success' : 'danger'" />
                        </template>
                    </Column>
                    <Column field="pentashih" header="Pentashih"></Column>
                    <Column field="tanggal" header="Tanggal" style="min-width: 13rem"></Column>
                    <template #groupheader="slotProps">
                        <div class="flex align-items-center gap-2">
                            <span class="text-bold"><strong>{{ slotProps.data.category }}</strong></span>
                        </div>
                    </template>
                </DataTable>
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
import { ref, onBeforeMount, reactive } from 'vue';
import { CustomerService } from '@/service/CustomerService';

const dataSantri = ref(null);

const dropdownValue = ref(null);
const dropdownValues = ref([
    { name: 'Tidak Tercapai' },
    { name: 'Tercapai' },
]);

const customerService = new CustomerService();


onBeforeMount(() => {
    customerService.getCustomersMedium().then((data) => (dataSantri.value = data));
    console.log(dataSantri.value);
});

</script>