<template>
    <div class="px-2 py-4">
        <h1 class="text-4xl font-bold">📝 Aplikasi Ujian Oji</h1>
    </div>
    <div class="flex flex-column gap-2  bg-black">
        <router-link to="/admin">
            <Button class="w-full" label="Admin" />
        </router-link>
        <router-link to="/pentashih">
            <Button class="w-full" label="Pentashih" />
        </router-link>
        <router-link to="/raport-santri">
            <Button class="w-full" label="Raport Santri" />
        </router-link>
    </div>
</template>