

<template>
    <div id="booking-container">
        <h2>Boka tid</h2>
        <div>
            <form @submit.prevent="addAppointment">
                <div class="flex">
                    <div>
                        <label>Barberare</label>
                        <select v-on:change="updateDates" v-model="this.barber" required>
                            <option
                                v-for="barber in barbers"
                                :key="barber.id"
                                :value="barber.barberId"
                            >{{ barber.barberName }} - {{ barber.role }}</option>
                        </select>
                    </div>

                    <div>
                        <label>Tjänst</label>
                        <select v-model="this.service" required>
                            <option
                                v-for="service in services"
                                :key="service.id"
                                :value="service.serviceId"
                            >{{ service.serviceName }}</option>
                        </select>
                    </div>
                </div>

                <label>Tillgängliga tider</label>
                <select v-model="this.date" required>
                    <option v-for="date in available" :key="date.id" :value="date">{{ date }}</option>
                </select>

                <button v-on:click="getLessDates" v-bind:class ="this.add < 7 ? 'no-click' : 'click'">Tidigare vecka</button>
                <button v-on:click="getMoreDates">Nästa vecka</button>

                <label>Namn</label>
                <input type="text" required v-model="this.name" />

                <label>Telefonnummer</label>
                <input type="text" required v-model="this.phone" />

                <button class="submit-btn" type="submit">boka</button>
            </form>
        </div>

        <!-- <p>Kundnamn: {{ this.name }}</p> -->
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
            phone: ""
        }
    },
    mounted() {
        fetch(this.barberUrl)
            .then(response => response.json())
            .then(data => {
                this.barbers = data;
            })
            .catch(error => {
                console.log("Error: ", error)
            }),
            fetch(this.serviceUrl)
                .then(response => response.json())
                .then(data => {
                    this.services = data;
                })
                .catch(error => {
                    console.log("Error: ", error)
                }),
            fetch(this.availableUrl + this.add + "/" + this.barber)
                .then(response => response.json())
                .then(data => {
                    this.available = data
                    this.date = this.available[0]

                    // this.available.forEach(item => {
                    //     this.availableList.push(item.split('T'))
                    // })

                    // this.date = this.availableList[0]


                })
                .catch(error => {
                    console.log("Error: ", error)
                })
    },
    methods: {
        getMoreDates: async function () {
            this.add += 7
            await fetch(this.availableUrl + this.add + "/" + this.barber)
                .then(response => response.json())
                .then(data => {
                    this.available = data
                    this.date = this.available[0]

                    // this.available.forEach(item => {
                    //     this.availableList.push(item.split('T'))

                    // })

                    // this.date = this.availableList[0]

                    // console.log(this.availableList)

                })
                .catch(error => {
                    console.log("Error: ", error)
                })
        },
        getLessDates: async function () {
            if (this.add >= 7) {
                this.add -= 7
                await fetch(this.availableUrl + this.add + "/" + this.barber)
                    .then(response => response.json())
                    .then(data => {
                        this.available = data
                        this.date = this.available[0]

                        // this.available.forEach(item => {
                        //     this.availableList.push(item.split('T'))

                        // })

                        // this.date = this.availableList[0]

                        // console.log(this.availableList)

                    })
                    .catch(error => {
                        console.log("Error: ", error)
                    })
            }

        },
        updateDates: async function () {
            await fetch(this.availableUrl + this.add + "/" + this.barber)
                .then(response => response.json())
                .then(data => {
                    this.available = ""
                    this.available = data

                    // this.available.forEach(item => {
                    //     this.availableList.push(item.split('T'))
                    // })

                    // this.date = this.availableList[0]
                })
                .catch(error => {
                    console.log("Error: ", error)
                })
        },
        addAppointment: async function () {
            let idCustomer = 0;
            console.log(idCustomer)

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
                    console.log("kund fanns redan: " + idCustomer)
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
                        console.log("Den här kunden har lagts till: " + data)
                        idCustomer = data.customerId
                        console.log("Ny kund: " + idCustomer)
                    })
                    .catch(error => {
                        console.log("Error: ", error)
                    })
            }

            // Sätt appointment-objekt
            let appointmentObj = { "DateAndTime": this.date, "BarberId": this.barber, "CustomerId": idCustomer, "ServiceId": this.service }
            console.log(appointmentObj)

            await fetch(this.appointmentUrl, {
                method: "POST",
                headers: { "content-type": "application/json" },
                body: JSON.stringify(appointmentObj),
            })
                .then(response => response.json())
                .then(data => {
                    console.log(data)
                })
                .catch(error => {
                    console.log("Error: ", error)
                })
        }
    }
}
</script>

<style>
    .no-click {
        color: grey;
        
    }
</style>