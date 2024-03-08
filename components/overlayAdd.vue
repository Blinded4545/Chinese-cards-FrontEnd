<template>
    <v-app class="bg-transparent flex justify-center content-center">
            <v-card class="rounded-xl" style="width: fit-content;">
                <div class="" style="margin-top: 5vmin;margin-bottom: 5vmin;margin-left: 10vmin;margin-right: 10vmin;">
                    <v-card-title primary-title>
                        Create a new card
                    </v-card-title>
                    <v-text-field
                        name="Word"
                        label="Word"
                        v-model="addWord"
                        :rules="[rules.required]"
                    ></v-text-field>
                    <v-text-field
                        name="Meaning 1"
                        label="Meaning 1"
                        v-model="addMeanings[0]"
                        :rules="[rules.required]"
                    ></v-text-field>
                    <v-text-field
                        name="Meaning 2"
                        label="Meaning 2"
                        v-model="addMeanings[1]"
                    ></v-text-field>
                    <v-text-field
                        name="Meaning 3"
                        label="Meaning 3"
                        v-model="addMeanings[2]"
                    ></v-text-field>
                    <v-text-field
                        name="Meaning 4"
                        label="Meaning 4"
                        v-model="addMeanings[3]"
                    ></v-text-field>
                    <v-text-field
                        name="Meaning 5"
                        label="Meaning 5"
                        v-model="addMeanings[4]"
                    ></v-text-field>
                    <div class="grid justify-center content-center">
                        <v-btn color="success" @click="submitNewCard">Create Card</v-btn>
                    </div>
                </div>
            </v-card>
    </v-app>
</template>

<script>

export default {
    name: "overlayAdd",
    props: {
    },
    data() {
        return {
            addWord: "",
            addMeanings: ["",null,null,null,null],
            rules:{
                required: value=> !!value || "Field is required"
            },
        }
    },
    methods: {
        submitNewCard(){
            let w = this.addWord
            let send = {"newWrd": w, "means": this.addMeanings};
            //console.log(send);
            this.$emit("add", send);
            this.addWord = "";
            this.addMeanings = ["",null,null,null,null];
        }
    },
}

</script>