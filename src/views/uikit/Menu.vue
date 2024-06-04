<script setup>
import { ref } from 'vue';

const nestedRouteItems = ref([
    {
        label: 'Personal',
        to: '/uikit/menu'
    },
    {
        label: 'Seat',
        to: '/uikit/menu/seat'
    },
    {
        label: 'Payment',
        to: '/uikit/menu/payment'
    },
    {
        label: 'Confirmation',
        to: '/uikit/menu/confirmation'
    }
]);
</script>

<template>
    <div class="grid p-fluid">
        <div class="col-12 md:col-6">
            <div class="card card-w-title">
                <h5>TabMenu</h5>
                <p>Steps and TabMenu are integrated with the same child routes.</p>
                <TabMenu :model="nestedRouteItems" />
                <router-view />
            </div>
        </div>
    </div>
</template>
