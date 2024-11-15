<script setup>
import { RouterView } from "vue-router";
import main.js
</script>

<script>
export default {
    data() {
        return {
            query: ""
        }
    },
    methods: {
        async searchButton() {
            const connection = new BareMux.BareMuxConnection("/baremux/worker.js")
            const wispUrl = (location.protocol === "https:" ? "wss" : "ws") + "://" + location.host + "/wisp/";
            const bareUrl = (location.protocol === "https:" ? "https" : "http") + "://" + location.host + "/bare/"
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
    <!--<RouterView />-->
    <iframe id="iframeWindow" class="iframeWindow"></iframe>
    <input type="text" id="urlInput" placeholder="Enter URL here" v-model="query">
    <button id="searchButton" @click.prevent="searchButton()">Search Text</button>
</template>
