<template>
    <div>
        <button @click="startSpeechRecognition">
           {{speech}}
        </button>
        <div style="background-color: aliceblue;">{{ output }}</div>
    </div>
</template>
  
<script>
export default {
    data() {
        return {
            output: '',
            speech: 'Speak'
        };
    },
    mounted() {
        // Check if browser supports SpeechRecognition
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
        startSpeechRecognition() {
            if (this.recognition) {
                this.recognition.start();
            } else {
                alert('Speech recognition is not supported in your browser.');
            }
        }
    }
};
</script>
  