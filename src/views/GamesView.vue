<script setup>
import { RouterView } from "vue-router";
</script>

<script>
    export default {
        data() {
            return {
                query: ""
            }
        },
        mounted() {
            const connection = new BareMux.BareMuxConnection("/baremux/worker.js")
            const wispUrl = (location.protocol === "https:" ? "wss" : "ws") + "://" + location.host + "/wisp/";
            const bareUrl = (location.protocol === "https:" ? "https" : "http") + "://" + location.host + "/bare/"
        },
        methods: {
            async search() {
                let url = this.query; // if no periods are detected in the input, search google instead
                let searchUrl = "https://www.google.com/search?q=";

                if (!url.includes(".")) {
                    url = searchUrl + encodeURIComponent(url);
                } else {
                    if (!url.startsWith("http://") && !url.startsWith("https://")) { // if no http or https is detected, add https automatically
                        url = "https://" + url;
                    }
                }
                if (!await connection.getTransport()) {
                    await connection.setTransport("/epoxy/index.mjs", [{ wisp: wispUrl }]);
                }
                iframeWindow.src = __uv$config.prefix + __uv$config.encodeUrl(url);
            }
        }
    }
</script>

<template>
    <iframe id="iframeWindow" class="iframeWindow"></iframe>
    <form @submit="search">
        <input type="text" id="urlInput" v-model="query" placeholder="Enter URL here">
        <button type="submit" id="searchButton">Search Text</button>
    </form>
</template>
