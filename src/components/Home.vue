<template>
    <div class="flex-container">
        <div class="row">
            <div class="content">
                <h3>Menggunakan axios</h3>
                <button @click="downloadFileAxios('success')">Download success</button>
                <button @click="downloadFileAxios('fail')">Download fail</button>
            </div>

            <div class="content">
                <h3>Menggunakan fetch</h3>
                <button @click="downloadFileFetch('success')">Download success</button>
                <button @click="downloadFileFetch('fail')">Download fail</button>
            </div>
        </div>
        <div class="error">
            {{ lastError }}
        </div>
    </div>

</template>

<script setup>
import axios from "axios"
import { ref } from "vue";

const URL_API = import.meta.env.VITE_URL_API;
const lastError = ref("")

const downloadFileAxios = async (path) => {
    try {
        lastError.value = ""
        var dt = await axios({
            url: `${URL_API}/file/${path}`,
            method: 'GET',
            // isi respone type dengan array buffer karene respone bisa file/blob atau json
            responseType: 'arraybuffer',
            transformResponse: (data, _, status) => {
                // ubah data menjadi tipe yang sesuai dengan melihat http status nya
                return status == 200 ? window.URL.createObjectURL(new Blob([data])) : JSON.parse(String.fromCharCode.apply(null, new Uint8Array(data)))
            }
        })

        const link = document.createElement('a');
        link.href = dt.data;
        link.setAttribute('download', 'berkas.pdf');
        document.body.appendChild(link);
        link.click();
    } catch (error) {
        lastError.value = error.response?.data?.message ?? "Gagal download file"
    }
}

const downloadFileFetch = async (path) => {
    try {
        lastError.value = ""
        var blob = null
        var response = await fetch(`${URL_API}/file/${path}`)

        if (response.ok) {
            blob = await response.blob()
        } else {
            throw response
        }
        const url = window.URL.createObjectURL(blob);
        const link = document.createElement('a');
        link.href = url;
        link.setAttribute('download', 'berkas.pdf');
        document.body.appendChild(link);
        link.click();
    } catch (error) {
        var e = await error.json()
        lastError.value = e.message ?? "Gagal download file"
    }
}
</script>

<style>
.flex-container {
    display: flex;
    flex-direction: column;
    justify-content: center;
    height: 100%;
    align-items: center;
}

.content {
    display: flex;
    flex-direction: column;
}

.content button {
    width: 200px;
    padding: 10px;
    margin: 10px;
}

.row {
    display: flex;
    flex-direction: row;
}

.error {
    color: red;
}
</style>