<template>
    <div class="grid bg-black">
        <div class="col-12 md:col-5">
            <div class="card p-fluid">
                <h3>Pentashih</h3>
                <div class="field">
                    <label for="pentashih">Nama</label>
                    <InputText v-model="checkSantriState.pentashih" id="pentashih" type="text" />
                    <small v-for="error of checkSantriValidate$.pentashih.$errors" :key="error.$uid">
                        {{ error.$messages }}</small>
                </div>
                <div class="field">
                    <label for="santri-code">Kode Santri</label>
                    <div class="flex flex-row gap-3">
                        <InputText v-model="checkSantriState.santriCode" id="santri-code" type="text" />
                        <Button @click="checkSantri" class="w-4 md:w-3" label="Cek"></Button>
                    </div>
                    <small v-for="error of checkSantriValidate$.santriCode.$errors" :key="error.$uid">
                        {{ error.$messages }}</small>
                </div>
            </div>
        </div>

        <div class="col-12 md:col-7">
            <div class="card p-fluid">
                <div v-if="!checked">
                    Tes
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
                            <TabPanel v-for="(item, index) in subject" :key="index" :header="item.title">
                                <div v-for="(subject, index) in item.children" :key="index" class="py-3">
                                    <h5>{{ subject.title }}</h5>
                                    <div class="formgrid md:grid">
                                        <div v-if="subject.has_setoran" class="field md:col-6">
                                            <label for="name2">Setoran</label>
                                            <Dropdown
                                                v-model="grades[subject.category_slug][subject.title_slug]['setoran']"
                                                :options="gradeValues" optionLabel="name" placeholder="Capaian" />
                                        </div>
                                        <div v-if="subject.has_hafalan" class="field md:col-5">
                                            <label for="email2">Hafalan</label>
                                            <Dropdown
                                                v-model="grades[subject.category_slug][subject.title_slug]['hafalan']"
                                                :options="gradeValues" optionLabel="name" placeholder="Capaian" />
                                        </div>
                                    </div>
                                </div>
                            </TabPanel>
                        </TabView>
                    </div>
                    <div class="flex flex-row-reverse">
                        <Button @click="submitGrade" label="Simpan" icon="pi pi-save" class="w-fit mr-2"></Button>
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
</style>

<script setup>
import { ref, reactive, computed, watch, onMounted } from 'vue';
import { supabase } from '../../../utils/supabase'
import { useVuelidate } from '@vuelidate/core'
import { required } from '@vuelidate/validators'

const pentashih = ref(null);
const checked = ref(false);
const santriCode = ref(null);
const santriName = ref(null);
const date = ref(null);
const subject = ref([])
const grades = ref({});
const gradeValues = ref([
    { name: 'Tidak Tercapai' },
    { name: 'Tercapai' },
]);

const checkSantriState = reactive({
    pentashih: '',
    santriCode: '',
})

const checkSantriRules = {
    pentashih: { required },
    santriCode: { required },
}

const checkSantriValidate$ = useVuelidate(checkSantriRules, checkSantriState)


async function checkSantri() {

    const isFormCorrect = await checkSantriValidate$.value.$validate();

    console.log(isFormCorrect);

    if (isFormCorrect) {
        const { data } = await supabase.from('santri').select().eq('code', checkSantriState.santriCode).single();
        santriName.value = data.name;
        date.value = new Date();
        checked.value = true;
    } else {
        alert(JSON.stringify(checkSantriValidate$.value.$errors))
    }

}

async function getSubject() {
    return await supabase.from('subject').select()
}

function mapData(data) {

    let restructuredData = {};
    let gradesRef = {};

    data.forEach(item => {
        if (!restructuredData[item.category]) {
            restructuredData[item.category] = [];
            gradesRef[item.category_slug] = {};
        }
        restructuredData[item.category].push({
            title: item.name,
            title_slug: item.name_slug,
            category_slug: item.category_slug,
            has_hafalan: item.has_hafalan,
            has_setoran: item.has_setoran,
        });
        gradesRef[item.category_slug][item.name_slug] = {
            hafalan: null,
            setoran: null,
        }
    });
    restructuredData = Object.keys(restructuredData).map(key => ({
        title: key,
        children: restructuredData[key]
    }));
    return [restructuredData, gradesRef]
}

function submitGrade() {
    console.log(JSON.stringify(grades.value));
}


onMounted(async () => {
    const { data } = await getSubject();
    [subject.value, grades.value] = mapData(data);
})

</script>