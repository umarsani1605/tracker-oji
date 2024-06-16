<template>
    <div class="px-2 py-4">
        <h1 class="text-4xl font-bold">📝 Raport Santri</h1>
    </div>
    <div class="grid bg-black">
        <div class="col-12">
            <div class="card flex justify-content-center p-fluid">
                <!-- <h3>Raport Santri</h3> -->
                <div class="field md:max-w-28rem">
                    <label for="santri-code">Kode Santri</label>
                    <div class="flex flex-row gap-3">
                        <InputText v-model="checkSantriValidate$.santriCode.$model"
                            :invalid="checkSantriValidate$.santriCode.$error" id="santri-code" type="text" />
                        <Button @click="checkSantri" class="w-4 md:w-5" label="Cek"></Button>
                    </div>
                    <small class="text-red-500" v-if="checkSantriValidate$.santriCode.$error">Kode harus diisi</small>
                </div>
            </div>
        </div>
        <div class="col-12">
            <div class="card p-fluid">
                <div v-if="!checked">
                    <h3>Santri</h3>
                    <Message severity="success" icon="pi pi-info-circle" :closable="false">
                        <span>Masukkan nama dan kode santri</span>
                    </Message>
                </div>
                <div v-else>
                    <h3>Capaian Ngaji</h3>
                    <div class="field my-4">
                        <table class="w-full">
                            <tr class="h-2rem">
                                <td>Nama</td>
                                <td>: <strong>{{ santriName }}</strong></td>
                            </tr>
                            <tr class="h-2rem">
                                <td>Angkatan</td>
                                <td>: <strong>{{ santriAngkatan }}</strong></td>
                            </tr>
                        </table>
                    </div>
                    <DataTable :value="santriGrades" rowGroupMode="subheader" groupRowsBy="subject.category"
                        sortMode="single" sortField="subject.category" :sortOrder="1" scrollable scrollHeight="400px"
                        showGridlines>
                        <Column field="subject.category" header="Representative"></Column>
                        <Column field="subject.name" header="Nama" style="min-width: 8rem"></Column>
                        <Column field="setoran" header="Setoran" style="min-width: 9rem">
                            <template #body="slotProps">
                                <Tag :value="slotProps.data.setoran"
                                    :severity="slotProps.data.setoran ? 'success' : 'danger'" />
                            </template>
                        </Column>
                        <Column field="hafalan" header="Hafalan" style="min-width: 9rem">
                            <template #body="slotProps">
                                <Tag :value="slotProps.data.hafalan ? 'Tercapai' : 'Tidak Tercapai'"
                                    :severity="slotProps.data.hafalan ? 'success' : 'danger'" />
                            </template>
                        </Column>
                        <Column field="pentashih" header="Pentashih"></Column>
                        <Column field="date" header="Tanggal" style="min-width: 13rem"></Column>
                        <template #groupheader="slotProps">
                            <div class="flex align-items-center gap-2">
                                <span class="text-bold"><strong>{{ slotProps.data.subject.category }}</strong></span>
                            </div>
                        </template>
                    </DataTable>
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

import { ref, onBeforeMount, reactive } from 'vue';
import { supabase } from '../../../utils/supabase'
import { useVuelidate } from '@vuelidate/core'
import { required } from '@vuelidate/validators'
import { CustomerService } from '@/service/CustomerService';

// cek kode santri

const checked = ref(false);

const checkSantriState = reactive({
    santriCode: '',
})
const checkSantriRules = {
    santriCode: { required },
}
const checkSantriValidate$ = useVuelidate(checkSantriRules, checkSantriState)

// cek nilai santri

const santriName = ref(null)
const santriAngkatan = ref(null)

const santriGrades = ref(null);

const customerService = new CustomerService();

async function checkSantri() {

    const isFormCorrect = await checkSantriValidate$.value.$validate();

    if (isFormCorrect) {

        const santri = await supabase.from('santri').select().eq('code', checkSantriState.santriCode).single();

        santriName.value = santri.data.name;
        santriAngkatan.value = santri.data.angkatan;

        const gradesRaw = await supabase.from('grade').select(`
            id_subject, id_santri, hafalan, setoran, pentashih, date, subject ( name, category, has_hafalan, has_setoran )
        `).eq('id_santri', santri.data.id);

        santriGrades.value = gradesRaw.data;

        checked.value = true;

    } else {
        // to do
    }
}

onBeforeMount(() => {
    // customerService.getCustomersMedium().then((data) => (santriGrades.value = data));
    // console.log(santriGrades.value);
});

</script>