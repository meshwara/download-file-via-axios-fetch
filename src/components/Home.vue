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
        var res = await axios({
            url: `${URL_API}/file/${path}`,
            method: 'GET',
            responseType: 'blob', // important
        }).then((response) => {
            const url = window.URL.createObjectURL(new Blob([response.data]));
            const link = document.createElement('a');
            link.href = url;
            link.setAttribute('download', 'berkas.pdf');
            document.body.appendChild(link);
            link.click();
        }).catch(e => {
            // di sini tipe error adalah blob, sehingga tidak bisa kita ubah menjadi json
            // maka, berikan error yang umum
            lastError.value = "Gagal download file"
        })

    } catch (error) {
        console.log(error)
    }
}

const downloadFileFetch = async (path) => {
    try {
        lastError.value = ""
        await fetch(`${URL_API}/file/${path}`)
            .then((response) => {
                if (response.ok) {
                    return response.blob()
                } else {
                    return Promise.reject(response)
                }
            })
            .then(blob => {
                const url = window.URL.createObjectURL(blob);
                const link = document.createElement('a');
                link.href = url;
                link.setAttribute('download', 'berkas.pdf');
                document.body.appendChild(link);
                link.click();
            })
            .catch(async (e) => {
                var data = await e.json()
                return Promise.reject(data)
            })
            .catch(e => {
                lastError.value = e.message
            })

    } catch (error) {
        console.log(error)
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