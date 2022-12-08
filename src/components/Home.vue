<template>
    <div class="flex-container">
        <div class="content">
            <button @click="downloadFile('success')">Download success</button>
            <button @click="downloadFile('fail')">Download fail</button>
        </div>
    </div>
</template>

<script setup>
import axios from "axios"
const URL_API = import.meta.env.VITE_URL_API;

const downloadFile=async (path)=>{
    try {
        var res = await axios.get(`${URL_API}/file/${path}`)
        console.log(res)
        
    } catch (error) {
        console.log(error.response.data)
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
</style>