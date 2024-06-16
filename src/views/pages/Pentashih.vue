<template>
    <div class="px-2 py-4">
        <h1 class="text-4xl font-bold">📝 Ujian Omah Ngaji</h1>
    </div>
    <div class="grid bg-black">
        <div class="col-12 md:col-5">
            <div class="card p-fluid">
                <h3>Pentashih</h3>
                <div class="field">
                    <label for="pentashih">Nama</label>
                    <InputText v-model="checkSantriValidate$.pentashih.$model" id="pentashih" type="text"
                        :invalid="checkSantriValidate$.pentashih.$error" />
                    <small class="text-red-600" v-if="checkSantriValidate$.pentashih.$error">Nama harus diisi</small>
                </div>
                <div class="field">
                    <label for="santri-code">Kode Santri</label>
                    <div class="flex flex-row gap-3">
                        <InputText v-model="checkSantriValidate$.santriCode.$model" id="santri-code" type="text"
                            :invalid="checkSantriValidate$.santriCode.$error" />
                        <Button @click="checkSantri" class="w-3 md:w-5" label="Cek"></Button>
                    </div>
                    <small class="text-red-500" v-if="checkSantriValidate$.santriCode.$error">Kode harus diisi</small>
                </div>
            </div>
        </div>

        <div class="col-12 md:col-7">
            <div class="card p-fluid">
                <div v-if="!checked">
                    <h3>Santri</h3>
                    <Message severity="success" icon="pi pi-info-circle" :closable="false">
                        <span>Masukkan nama dan kode santri</span>
                    </Message>
                </div>
                <div v-else>
                    <h3>Santri</h3>
                    <div class="formgrid mb-5 md:grid">
                        <div class="field md:col-7">
                            <label for="santri-name">Nama</label>
                            <InputText v-model="santriName" id="santri-name" type="text" placeholder="Nama Santri"
                                disabled />
                        </div>
                        <div class="field md:col-5">
                            <label for="date">Tanggal</label>
                            <InputGroup>
                                <Calendar inputId="calendar" v-model="date" touchUI></Calendar>
                                <InputGroupAddon>
                                    <i class="pi pi-calendar"></i>
                                </InputGroupAddon>
                            </InputGroup>
                        </div>
                    </div>
                    <Divider class="my-3" />
                    <div class="">
                        <TabView :scrollable="true">
                            <TabPanel v-for="(category, index) in grades" :key="index"
                                :header="category[0].subject_category">
                                <div v-for="(subject, index) in category" :key="index" class="py-3">
                                    <h5>{{ subject.subject_name }}</h5>
                                    <div class="formgrid md:grid">
                                        <div v-if="subject.has_setoran" class="field md:col-5">
                                            <label for="setoran">Setoran</label>
                                            <Dropdown v-model="gradesState[subject.id_grade]['setoran']"
                                                :options="gradeValues" optionLabel="grade"
                                                placeholder="Pilih Capaian" />
                                        </div>
                                        <div v-if="subject.has_hafalan" class="field md:col-5">
                                            <label for="hafalan">Hafalan</label>
                                            <Dropdown v-model="gradesState[subject.id_grade]['hafalan']"
                                                :options="gradeValues" optionLabel="grade"
                                                placeholder="Pilih Capaian" />
                                        </div>
                                    </div>
                                </div>
                            </TabPanel>
                        </TabView>
                    </div>
                    <ConfirmDialog></ConfirmDialog>
                    <div class="flex flex-row-reverse">
                        <Button @click="konfirmasi()" label="Simpan" icon="pi pi-save" class="w-fit mr-2"></Button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style>
.p-tabview-panels {
    padding: 1rem 0.25rem;
}

.p-dialog-header {
    color: #10b981;
}

.p-dialog {
    margin: 0 1.5rem !important;
}
</style>

<script setup>
import { ref, reactive, computed, watch, onMounted } from 'vue';
import { supabase } from '../../../utils/supabase'
import { useVuelidate } from '@vuelidate/core'
import { required } from '@vuelidate/validators'
import { useConfirm } from "primevue/useconfirm";
import { useToast } from "primevue/usetoast";
import dateFormat, { i18n } from 'dateformat'

const checked = ref(false);
const santriName = ref(null);
const date = ref(null);
const grades = ref({});
const gradeValues = ref([
    { grade: 'Tidak Tercapai' },
    { grade: 'Tercapai' },
]);
const gradesState = ref({});

const checkSantriState = reactive({
    pentashih: '',
    santriCode: '',
})

const checkSantriRules = {
    pentashih: { required },
    santriCode: { required },
}

i18n.dayNames = [
    "Min",
    "Sen",
    "Sel",
    "Rab",
    "Kam",
    "Jum",
    "Sab",
    "Ahad",
    "Senin",
    "Selasa",
    "Rabu",
    "Kamis",
    "Jum'at",
    "Sabtu",
];

i18n.monthNames = [
    "Jan",
    "Feb",
    "Mar",
    "Apr",
    "Mei",
    "Jun",
    "Jul",
    "Agu",
    "Sep",
    "Okt",
    "Nov",
    "Des",
    "Januari",
    "Februari",
    "Maret",
    "April",
    "Mei",
    "Juni",
    "Juli",
    "Agustus",
    "September",
    "Oktober",
    "November",
    "Desember",
];

const checkSantriValidate$ = useVuelidate(checkSantriRules, checkSantriState)

async function checkSantri() {

    const isFormCorrect = await checkSantriValidate$.value.$validate();

    if (isFormCorrect) {

        const santri = await supabase.from('santri').select().eq('code', checkSantriState.santriCode).single();

        santriName.value = santri.data.name;
        date.value = new Date();

        checked.value = true;

        const gradesRaw = await getSubject(santri.data.id);
        [gradesState.value, grades.value] = await restructureData(gradesRaw.data);

    } else {
        // to do
    }
}

async function getSubject(idSantri) {
    return await supabase.from('grade').select(`
        id, id_santri, id_subject, subject ( name, category, has_hafalan, has_setoran )
    `).eq('id_santri', idSantri);
}

async function submitGrade() {

    let upsertData = []

    Object.keys(gradesState.value).forEach(key => {

        upsertData.push({
            id: key,
            setoran: gradesState.value[key].setoran.grade,
            hafalan: gradesState.value[key].hafalan.grade,
            pentashih: checkSantriState.pentashih.replace(/"/g, ''),
            date: dateFormat(new Date(), "dddd, d mmmm yyyy"),
        })
    });

    return await supabase.from('grade').upsert(upsertData);
}

async function restructureData(data) {

    let restructuredData = {};
    let gradeRef = {};

    data.forEach(item => {
        if (!restructuredData[item.subject.category]) {
            restructuredData[item.subject.category] = [];
        }
        restructuredData[item.subject.category].push({
            id_grade: item.id,
            id_santri: item.id_santri,
            id_subject: item.id_subject,
            subject_name: item.subject.name,
            subject_category: item.subject.category,
            has_hafalan: item.subject.has_hafalan,
            has_setoran: item.subject.has_setoran,
        });
        gradeRef[item.id] = {
            setoran: {
                grade: null
            },
            hafalan: {
                grade: null
            },
        }
    });
    // restructuredData = Object.keys(restructuredData).map(key => ({
    //     title: key,
    //     children: restructuredData[key]
    // }));
    return [gradeRef, restructuredData];
}

const confirm = useConfirm();
const toast = useToast();

const konfirmasi = () => {
    confirm.require({
        message: 'Apakah kamu sudah yakin? <br> Mohon koreksi kembali sebelum menyimpan.',
        header: 'Konfirmasi',
        icon: 'pi pi-exclamation-circle',
        rejectProps: {
            label: 'Batalkan',
            severity: 'secondary',
            outlined: true
        },
        acceptProps: {
            label: 'Simpan'
        },
        accept: () => {
            submitGrade();
            toast.add({ severity: 'success', summary: 'Data disimpan!', life: 3000 });
        },
    });
};

onMounted(async () => {
    // const { data } = await getSubject();
    // [subject.value, grades.value] = mapData(data);
})

</script>