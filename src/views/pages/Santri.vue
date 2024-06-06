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
                <DataTable :value="customer3" rowGroupMode="subheader" groupRowsBy="representative.name" sortMode="single" sortField="representative.name" :sortOrder="1" scrollable scrollHeight="400px">
                    <Column field="representative.name" header="Representative"></Column>
                    <Column field="name" header="Name"></Column>
                    <Column field="country" header="Country">
                        <template #body="{ data }">
                            <div class="flex align-items-center gap-2">
                                <img alt="flag" src="/demo/images/flag/flag_placeholder.png" :class="`flag flag-${data.country.code}`" style="width: 24px" />
                                <span>{{ data.country.name }}</span>
                            </div>
                        </template>
                    </Column>
                    <template #groupheader="slotProps">
                        <div class="flex align-items-center gap-2">
                            <img :alt="slotProps.data.representative.name" :src="'/demo/images/avatar/' + slotProps.data.representative.image" width="32" style="vertical-align: middle" />
                            <span>{{ slotProps.data.representative.name }}</span>
                        </div>
                    </template>
                    <template #groupfooter="slotProps">
                        <td style="text-align: right" class="text-bold pr-6">Total Customers: {{ calculateCustomerTotal(slotProps.data.representative.name) }}</td>
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

const customer3 = ref(null);
const date = ref(null);

const dropdownValue = ref(null);
const dropdownValues = ref([
    { name: 'Tidak Tercapai' },
    { name: 'Tercapai' },
]);

const customerService = new CustomerService();


onBeforeMount(() => {
    customerService.getCustomersMedium().then((data) => (customer3.value = data));
});

const data = {
    data: [
        {
            category: "Al-Qur'an",
            name: "Juz Amma",
            hafalan: true,
        }
    ]
}

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