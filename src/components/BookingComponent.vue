<template>
    <div id="booking-container">
        <h2>Boka tid</h2>
        <div>
            <!-- Bokningsformulär -->
            <form @submit.prevent="addAppointment">
                <div class="flex">
                    <div>
                        <label>Barberare</label>
                        <div class="select-container">
                            <select v-on:change="updateDates" v-model="this.barber" required>
                                <option
                                    v-for="barber in barbers"
                                    :key="barber.id"
                                    :value="barber.barberId"
                                >{{ barber.barberName }} - {{ barber.role }}</option>
                            </select>
                        </div>
                    </div>

                    <!-- Tjänster -->
                    <div>
                        <label>Tjänst</label>
                        <div class="select-container">
                            <select v-model="this.service" required>
                                <option
                                    v-for="service in services"
                                    :key="service.id"
                                    :value="service.serviceId"
                                >{{ service.serviceName }}({{service.serviceDuration}} min) - {{service.servicePrice}}kr</option>
                            </select>
                        </div>
                    </div>
                </div>

                <!-- Tillgängliga tider -->
                <div class="dates-container">
                    <label>Tillgängliga tider</label>
                    <div class="select-container">
                        <select v-model="this.date" required>
                            <option v-for="date in available" :key="date.id" :value="date">{{ date }}</option>
                        </select>
                    </div>

                    <!-- Gör knappen grå om den inte kan backa mer -->
                    <button v-on:click="getLessDates" v-bind:class ="this.add < 7 ? 'no-click btn-dates' : 'btn-dates'">&#60; Tidigare vecka</button>
                    <button class="btn-dates" v-on:click="getMoreDates">Nästa vecka &#62;</button>
                </div>

                <!-- Kundens personuppgifter -->
                <label>Ditt för- och efternamn</label>
                <input type="text" required v-model="this.name" />

                <label>Ditt telefonnummer</label>
                <input type="text" required v-model="this.phone" />

                <button class="submit-btn" type="submit">boka</button>
            </form>
        </div>

        <!-- Meddelande -->
        <div v-bind:class="this.msg == '' ? 'hide' : 'thank-you'">{{this.msg}}</div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            barberUrl: "https://localhost:7290/API/Barber/",
            serviceUrl: "https://localhost:7290/API/Service/",
            availableUrl: "https://localhost:7290/API/AvailableAppointments/",
            appointmentUrl: "https://localhost:7290/API/Appointment/",
            customerUrl: "https://localhost:7290/API/Customer/",
            barbers: {},
            services: {},
            available: {},
            availableList: [],
            customers: [],
            add: 0,
            barber: 1,
            service: 1,
            date: "",
            name: "",
            phone: "",
            bookingForm: 0,
            msg: ""
        }
    },
    mounted() {
        // Hämta alla barberare
        fetch(this.barberUrl)
            .then(response => response.json())
            .then(data => {
                this.barbers = data;
            })
            .catch(error => {
                console.log("Error: ", error)
            }),
            // Hämta alla tjänster
            fetch(this.serviceUrl)
                .then(response => response.json())
                .then(data => {
                    this.services = data;
                })
                .catch(error => {
                    console.log("Error: ", error)
                }),
                // Hämta alla tillgängliga tider med add = 0 och barberId är det som är i formuläret
            fetch(this.availableUrl + this.add + "/" + this.barber)
                .then(response => response.json())
                .then(data => {
                    this.available = data
                    this.date = this.available[0]
                })
                .catch(error => {
                    console.log("Error: ", error)
                })
    },
    methods: {
        getMoreDates: async function () {
            // Addera add med 7 och hämta tillgängliga tider på nytt
            this.add += 7
            await fetch(this.availableUrl + this.add + "/" + this.barber)
                .then(response => response.json())
                .then(data => {
                    this.available = data
                    this.date = this.available[0]
                })
                .catch(error => {
                    console.log("Error: ", error)
                })
        },
        getLessDates: async function () {
            // Subtrahera add med 7 om add är större än eller lika med 7, och hämta tillgängliga tider
            if (this.add >= 7) {
                this.add -= 7
                await fetch(this.availableUrl + this.add + "/" + this.barber)
                    .then(response => response.json())
                    .then(data => {
                        this.available = data
                        this.date = this.available[0]
                    })
                    .catch(error => {
                        console.log("Error: ", error)
                    })
            }

        },
        updateDates: async function () {
            // Hämta tillgängliga tider för ny barberare
            await fetch(this.availableUrl + this.add + "/" + this.barber)
                .then(response => response.json())
                .then(data => {
                    this.available = ""
                    this.available = data
                })
                .catch(error => {
                    console.log("Error: ", error)
                })
        },
        addAppointment: async function () {
            let idCustomer = 0;
            console.log(idCustomer)

            // Hämta alla kunder
            await fetch(this.customerUrl)
                .then(response => response.json())
                .then(data => {
                    this.customers = data
                })
                .catch(error => {
                    console.log("Error: ", error)
                })

            console.log(this.customers)

            // Finns kunden redan registrerad? hämta id
            this.customers.forEach(customer => {
                if (customer.customerName == this.name && customer.phone == this.phone) {
                    idCustomer = customer.customerId
                }
            })

            // Om kund inte fanns registrerad, lägg till kund och sätt id
            if (idCustomer == 0) {
                let customerObj = { "CustomerName": this.name, "Phone": this.phone }

                await fetch(this.customerUrl, {
                    method: "POST",
                    headers: { "content-type": "application/json" },
                    body: JSON.stringify(customerObj),
                })
                    .then(response => response.json())
                    .then(data => {
                        idCustomer = data.customerId
                    })
                    .catch(error => {
                        console.log("Error: ", error)
                    })
            }

            // Sätt appointment-objekt
            let appointmentObj = { "DateAndTime": this.date, "BarberId": this.barber, "CustomerId": idCustomer, "ServiceId": this.service }

            // POST till API
            await fetch(this.appointmentUrl, {
                method: "POST",
                headers: { "content-type": "application/json" },
                body: JSON.stringify(appointmentObj),
            })
                .then(response => response.json())
                .then(data => {
                    this.msg = "Tack " + this.name + " för din bokning!"
                    // Ladda om sidan
                    setTimeout(() => {location.reload()}, 3000)
                    
                })
                .catch(error => {
                    console.log("Error: ", error)
                })
        }
    }
}
</script>

<style>
</style>