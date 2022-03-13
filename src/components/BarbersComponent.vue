<template>
    <div>
        <h2>Här visas barberare</h2>
        <div v-for="barber in barbers" :key="barber.id" class="barber">
            <li>{{barber.barberName}} - {{barber.role}}</li>
        </div>

    </div>
</template>

<script>
export default {
    data(){
        return {
            url: "https://localhost:7290/API/Barber/",
            barbers: {}
        }
    },
    mounted() {
        fetch(this.url)
            .then(response => response.json())
            .then(data => {
                this.barbers = data;
            })
            .catch(error => {
                console.log("Error: ", error)
            })
    }
}
</script>