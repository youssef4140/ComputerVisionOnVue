<template>
    <div>
        <!-- <info class="btn41-43 btn-43">
            info
        </info> -->

        <button @click="toggleSpeechRecognition" class="btn41-43 btn-43">
           {{speech}}
        </button>        
    </div>
</template>
  
<script>
import info from "./Info.vue";

export default {
    data() {
        return {
            output: '',
            speech: 'Speak',
            isRecognitionOn : false
        };
    },
    mounted() {
        this.recognition = window.SpeechRecognition || window.webkitSpeechRecognition;
        if (typeof this.recognition === 'undefined') {
            alert('Sorry! Speech recognition is not supported in your browser.');
        } else {
            this.recognition = new this.recognition();
            this.recognition.lang = 'en-US';
            this.recognition.onstart = () => {
                this.speech = 'Listening...';
            };

            this.recognition.onresult = event => {
                const transcript = event.results[0][0].transcript;
                this.output = transcript;
                if(transcript.includes("what is this")) this.$emit('CV','whatIsThis');
                if(transcript.includes("is this a")) this.$emit('CV','isThisA');
                this.speech = 'Speak'
            };

            this.recognition.onerror = event => {
                this.output = 'Error occurred in recognition: ' + event.error;
                this.speech = 'Speak'
            };
        }
    },
    methods: {
        toggleSpeechRecognition() {
            if (this.recognition) {
                if(this.isRecognitionOn == false) {
                    this.recognition.start();
                    this.isRecognitionOn = true
                } else if (this.isRecognitionOn == true) {
                    this.recognition.abort();
                    this.isRecognitionOn = false;
                }
                
            } else {
                alert('Speech recognition is not supported in your browser.');
            }
        }
    }
};
</script>
  

<style scoped>
.btn41-43 {
  padding: 10px 25px;
  font-family: "Roboto", sans-serif;
  font-weight: 500;
  background: transparent;
  outline: none !important;
  cursor: pointer;
  transition: all 0.3s ease;
  position: relative;
  display: inline-block;
}

.btn-43 {
  border: 2px solid black;
  z-index: 1;
  color: black;
}

.btn-43:after {
  position: absolute;
  content: "";
  width: 100%;
  height: 0;
  top: 0;
  left: 0;
  z-index: -1;
  background: black;
  transition: all 0.3s ease;
}

.btn-43:hover {
  color: white;
}

.btn-43:hover:after {
  top: auto;
  bottom: 0;
  height: 100%;
}
</style>