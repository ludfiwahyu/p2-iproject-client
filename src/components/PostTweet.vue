<template>
    <div>
            <div class="wrapper">
                <div class="input-box">
                <div  class="tweet-area">
                    <span class="placeholder"></span>
                    <textarea name="" id="" cols="80" rows="10" 
                        v-model="content" class="input editable" contenteditable="true" spellcheck="false"
                        style="width: auto; border: none" 
                    ></textarea>
                    <!-- <div type="text" :v-model="content" class="input editable" contenteditable="true" spellcheck="false"></div> -->
                    <div class="input readonly" contenteditable="true" spellcheck="false"></div>
                </div>
                <div class="privacy">
                    <i class="fas fa-globe-asia"></i>
                    <span>Everyone can reply</span>
                </div>
                </div>
                <div class="bottom">
                <ul class="icons">
                    <!-- <li><i class="uil uil-capture"></i></li>
                    <li><i class="far fa-file-image"></i></li> -->
                    <!-- <li><i class="far fa-grin"></i></li>
                    <li><i class="far fa-user"></i></li> -->
                    <li>

                        <i class="fas fa-map-marker-alt "></i>
                    </li>
                    <li>
                        <i @click="startSpeechToTxt" class="fas fa-microphone" ></i>

                    </li>
                    <li>
                        <i @click="postQuote" class="fas fa-quote-right"></i>
                    </li>
                </ul>
                <!-- <div class="content" type="button" >
                    <span class="counter">100</span>
                    
                </div> -->
                </div>
                    <button type="button" @click.prevent="doPostTweet(content)" class="btn btn-info">Tweet</button>
                    
            </div>
            <!-- <Map></Map> -->

    </div>
</template>

<script>
// import Map from './Map.vue'
export default {
    name: "PostTweet",
    componenst: {
        // Map
    },
    data: function () {
        return {
            content: "",
            location: "",
            runtimeTranscription_: "",
            transcription_: [],
            lang_: "en-US"
        }
    },
    methods : {
        doPostTweet: function (content) {
            const payload = {
                content: this.content,
                location: this.location
            }
            this.$store.dispatch('postTweet', payload)
            .then(({data}) => {
                this.$router.push('/')
                console.log(data);
                this.$store.commit("SET_TWEETS", content)
            })
            .catch ((err) => {
                console.log(err.response.data);
            })
        },
        startSpeechToTxt() {
            // initialisation of voicereco
        
            window.SpeechRecognition =
            window.SpeechRecognition || 
            window.webkitSpeechRecognition;
            const recognition = new window.SpeechRecognition();
            recognition.lang = this.lang_;
            recognition.interimResults = true;

            // event current voice reco word
            recognition.addEventListener("result", event => {      
                var text = Array.from(event.results)
                    .map(result => result[0])
                    .map(result => result.transcript)
                    .join("");
                this.runtimeTranscription_ = text;
                this.content = text;
            });
            // end of transcription
            recognition.addEventListener("end", () => {
                this.transcription_.push(this.runtimeTranscription_);
                this.runtimeTranscription_ = "";
                recognition.stop();
            });
            recognition.start();
        },
        postQuote () {
            this.content = this.$store.state.quotes
        }
    },
    computed: {
        quotes() {
            return this.$store.state.quotes;
        }
    },
    created() {
        this.$store.dispatch("getQuote")
    },
    


}

import "../assets/css/tweet.css"
</script>

<style>

</style>