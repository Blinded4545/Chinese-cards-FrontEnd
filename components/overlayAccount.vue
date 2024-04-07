<template>
    <div>
        <div>
            
            <v-card class="h-100" v-if="loggedInAcc">
                <v-card-title primary-title>
                    Camilo Herrera
                </v-card-title>
                <v-card-text>
                    wolaaaaas
                </v-card-text>

                <v-btn color="error" @click="logoutAccount">Logout</v-btn>
            </v-card>

            <v-card class="h-100" v-else>
                <div class="p-5  w-100">  
                    <v-form v-if="!register" ref="LogIn" @keyup.enter="loginAccount">
                        <v-card-title primary-title>
                            No has iniciado sesion
                        </v-card-title>
                        <v-card-text>
                            En la siguiente seccion puedes iniciar sesion
                        </v-card-text>
                        <v-text-field
                            label="Username"
                            v-model="usr"
                            required
                        ></v-text-field>
                        <v-text-field
                            label="Password"
                            v-model="pwd"
                            required
                        ></v-text-field>
                        <div class="flex justify-around">
                            <v-btn @click="loginAccount" color="success">Log in</v-btn>
                            <v-btn @click="register=true">Register</v-btn>
                        </div>
                    </v-form>
                    
                    <v-form v-else ref="form">
                    
                        <v-card-title primary-title>
                            Registrate aqui!
                        </v-card-title>
                        <v-card-text>
                            Rellena el siguiente formulario para registrate
                        </v-card-text>
                        <div>
                            <v-text-field
                                label="Username"
                                v-model="usr"
                                :counter="10"
                                required
                            ></v-text-field>
                            <v-text-field
                                label="Password"
                                v-model="pwd"
                                required
                            ></v-text-field>
                        
                            <div class="flex justify-around">
                                <v-btn @click="registerAccount" color="success">Register</v-btn>
                                <v-btn @click="register=false">Log in</v-btn>
                            </div>
                        </div>
                    </v-form>


                </div>
            </v-card>
        </div>
    </div>
</template>

<script>

export default {
    name: "overAcc",
    props:{
        loggedInAcc: Boolean
    },
    data() {
        return {
            register: false,
            usr: "",
            pwd: "",                         
            //nameRules: [v => !!v || "Name is required", v => (v && v.length <= 10) || "Name must be less than 10 characters"],
            //emailRules: [v => !!v || "E-mail is required", v => /^w+([.-]?w+)*@w+([.-]?w+)*(.w{2,3})+$/.test(v) || "E-mail must be valid"],
            wordCount: 0,
            accDate: "28-07-2024",
        }
    },
    methods: {
        async loginAccount(){
            if(this.usr!="" && this.pwd!=""){
                await $fetch('http://localhost:8000/login', {
                    method: "POST",
                    Headers:{
                        "Content-type": "application/json",
                    },
                    credentials: "include",
                    body:JSON.stringify({"usr": this.usr, "pwd": this.pwd})
                });
                this.$emit("logging", true);
            }
        },

        async registerAccount(){
            if(this.usr!="" && this.pwd!=""){
                $fetch("http://localhost:8000/reg", {
                    method: "POST",
                    Headers: {"Content-type": "application/json"},
                    body: JSON.stringify({"usr": this.usr, "pwd": this.pwd})
                })
            }
        },

        logoutAccount(){
            this.$emit("logout");
        }
    },
}

</script>
