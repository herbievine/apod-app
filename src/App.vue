<template>
    <div id="app">
        <div v-if="typeof imageData.title != 'undefined'">
            <h1 class="title">Astronomy Picture of the Day</h1>
            <p class="title">{{ imageData.title }} - {{ imageData.copyright ? imageData.copyright : 'No Author' }}</p>
            <p class="date">{{ imageData.date.split('-').reverse().join('/') }}</p>
            <p class="desc">{{ imageData.explanation }}</p>
            <div class="container">
                <button class="button" @click="() => {changeImage(this.date, '-')}">
                    <span style="font-size: 48px; color: whitesmoke;">
                        <i class="fas fa-chevron-left"></i>
                    </span>
                </button>
                <a :href="imageData.url" target="_BLANK">
                    <img :src="imageData.url" alt="" class="image">
                </a>
                <button class="button" @click="() => {changeImage(this.date, '+')}">
                    <span style="font-size: 48px; color: whitesmoke;">
                        <i class="fas fa-chevron-right"></i>
                    </span>
                </button>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'App',
        data() {
            return {
                api_key: 'API_KEY',
                base_url: 'https://api.nasa.gov/planetary/apod',
                imageData: {},
                date: ''
            }
        },
        methods: {
            fetchImage(searchDate) {
                const now = new Date();
                let date = {};

                if (searchDate === undefined) {
                    date = {
                        year: now.getFullYear(),
                        month: now.getMonth() + 1,
                        date: now.getDate(),
                    };
                } else {
                    const newDate = searchDate.split('-');
                    date = {
                        year: newDate[0],
                        month: newDate[1],
                        date: newDate[2],
                    };
                }

                fetch(`${this.base_url}?api_key=${this.api_key}&date=${date.year}-${date.month}-${date.date}`)
                .then(res => {
                    return res.json()
                })
                .then(res => {
                    this.imageData = res;
                    this.date = res.date;
                })
            },
            changeImage(date, diff) {
                const newDate = new Date(date);

                if (diff === '-') {
                    newDate.setDate(newDate.getDate() - 1);
                } else {
                    newDate.setDate(newDate.getDate() + 1);
                }

                const newDateObj = {
                    year: newDate.getFullYear(),
                    month: newDate.getMonth() + 1,
                    date: newDate.getDate(),
                };

                this.fetchImage(`${newDateObj.year}-${newDateObj.month}-${newDateObj.date}`);
            }
        },
        created() {
            this.fetchImage()
        }
    }
</script>

<style>
    @import url('https://fonts.googleapis.com/css2?family=Open+Sans:wght@400;600&display=swap');

    * {
        box-sizing: border-box;
    }
    html {
        margin: 0;
        padding: 0;
        width: 100%;
        height: 100vh;
    }
    body {
        margin: 0;
        padding: 0;
        width: 100%;
        height: 100vh;
        background-color: #101010;
    }
    input:focus, button:focus, textarea:focus {
        outline-width: 0;
    }
    button {
        cursor: pointer;
        border: none;
    }
    #app {
        font-family: 'Open Sans', sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: whitesmoke;
        margin: 60px 5%;
        width: 90%;
    }
    .title {
        text-align: center;
        font-weight: 600;
    }
    .date {
        text-align: center;
        font-weight: 400;
    }
    .desc {
        text-align: justify;
        font-weight: 400;
    }
    .container {
        display: flex;
        justify-content: center;
        max-height: 500px;
        min-height: 200px;
    }
    .image {
        background-size: cover;
        width: auto;
        max-width: 100%;
        max-height: 100%;
    }
    .button {
        height: 100%;
        background-color: #101010;
        margin: auto 0;
    }
</style>
