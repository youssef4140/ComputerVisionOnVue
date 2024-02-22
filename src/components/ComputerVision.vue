<template>
</template>
  
<script>
import axios from 'axios';
import FormData from 'form-data';

export default {
    data() {
        return {
            apiCredentials : {
                    key: 'acc_f99794812f8fb25',
                    secret: 'f400676cb91b3693704c87e397acde5d'
                }
        };
    },
    props: ['file'],
    watch: {
        file(newFile) {
            // console.log('File received in Vision component:', newFile);
            this.uploadImage(newFile);
        }
    },
    methods: {
        async uploadImage(file) {
            try {

                const formData = new FormData();
                formData.append('image', file, file.name);

           

                const response = await axios.post('https://api.imagga.com/v2/uploads', formData, {
                    headers: {
                        'Content-Type': 'multipart/form-data',
                        Authorization: `Basic ${btoa(`${this.apiCredentials.key}:${this.apiCredentials.secret}`)}`
                    }
                });

                this.getImageTags(response.data.result.upload_id);
                // console.log(response.data.result.upload_id);
            } catch (error) {
                console.error('Error:', error);
            }
        },
        async getImageTags(id) {
            try {
             

                const response = await axios.get(`https://api.imagga.com/v2/tags?image_upload_id=${id}`, {
                    headers: {
                        Authorization: `Basic ${btoa(`${this.apiCredentials.key}:${this.apiCredentials.secret}`)}`
                    }
                });
                console.log(response.data.result.tags);
                let sentence = this.constructSentence(response.data.result.tags)

                this.say(sentence);
            } catch (error) {
                console.error(error);
            }
        },
        constructSentence(tags) {
            let sentence = '';
            for (let i = 0; i < 4; i++) {
                if (sentence && i < 3) {
                    sentence += `, a ${Math.round(tags[i].confidence)} percent chance that this is a ${tags[i].tag.en}`;
                } else if (sentence && i === 3) {
                    sentence += ` and a ${Math.round(tags[i].confidence)} percent chance that this is a ${tags[i].tag.en}`;
                } else {
                    sentence += `there is a ${Math.round(tags[i].confidence)} percent chance that this is a ${tags[i].tag.en}`;
                }
            }
            return sentence;
        },
        say(sentence) {

            if ('speechSynthesis' in window) {

                let speakData = new SpeechSynthesisUtterance();
                speakData.volume = 1;
                speakData.rate = 1;
                speakData.pitch = 2;
                speakData.text = sentence;
                speakData.lang = 'en';
                speakData.voice = this.getVoices()[2];

                speechSynthesis.speak(speakData);

            } else {
                console.log('speech is not allowed in your browser')
            }
        },
        getVoices() {
            let voices = speechSynthesis.getVoices();
            if (!voices.length) {
                let utterance = new SpeechSynthesisUtterance("");
                speechSynthesis.speak(utterance);
                voices = speechSynthesis.getVoices();
            }
            return voices;
        }

    }
}
</script>

  