<template>
    <v-app style="min-height: 0;">
        <v-card @click="clicked" >
            <div class="h-28 bg-purple-200">
                <v-card-title primary-title>
                    {{ word }}
                </v-card-title>
                <v-card-text>
                    {{ meanings[0] }}
                </v-card-text>
            </div>
        </v-card>

        <v-overlay v-model="overlay" class="grid justify-center align-center w-screen h-screen">
      
            <v-card id="cardOverlay">
                <v-card-title primary-title>
                    <h1  class="text-5xl mt-2">{{ word }}</h1>
                </v-card-title>
                <ul>
                    <li v-for="(i,k) in meanings" :key="k">
                        <v-card-text class="py-2">
                            <h3 class="text-xl">{{ i }}</h3>
                        </v-card-text>
                    </li>
                </ul>
                <div class="w-full grid justify-center">
                    <v-btn color="error" @click="deleteCard()">Delete</v-btn>
                </div>
            </v-card>
        
        </v-overlay>
    </v-app>
</template>

<script>

export default {
    name: "cardGen",
    props: {
        "word": String,
        "meaning": Array
    },
    data(){
        return{
            currWord: this.word,
            meanings: this.meaning,
            overlay: false
        }
    },
    methods: {
        clicked(){
            console.log(this.currWord, this.meanings);
            this.overlay = !this.overlay;
        },
        async deleteCard(){
            const request = await $fetch('http://localhost:8000/delete', {
                method: "POST",
                Headers:{"Content-type": "application/json"},
                body:JSON.stringify({"word": this.currWord})
            });
            this.overlay=!this.overlay;
            this.$emit("deleted", this.currWord);
        }
    }
}

</script>

<style>

#cardOverlay{
    min-width: 60vmin;
    min-height: 60vmin;
    height: 50vmin;
    width: 50vmin;
}


</style>