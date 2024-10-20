<template>
  <div class="flex flex-col items-center justify-center  bg-gray-100 p-4">
    <!-- Start Speech Recognition Button -->
    <button
      @click="startRecognition"
      class="px-6 py-3 bg-blue-500 text-white font-semibold rounded-lg shadow-md hover:bg-blue-700 transition-all duration-300 ease-in-out focus:outline-none"
    >
      Огози барнома 
    </button>

    <!-- Recognized Text with Animated Display -->
    <div
      v-if="recognizedText"
      class="recognized-text mt-6 p-4 text-xl text-center text-gray-800 bg-white rounded-lg shadow-lg max-w-lg"
    >
      <div v-html="animatedText"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: "HomeView",
  data() {
    return {
      recognizedText: "", // The full recognized text
      animatedText: "", // The text displayed smoothly letter by letter
      recognition: null,
      interval: null,
      delay: 100, // Adjust the delay for letter-by-letter display
    };
  },
  mounted() {
    console.log(process.env.VUE_APP_OPENAI_API_KEY);
  },
  methods: {
    startRecognition() {
      if ("webkitSpeechRecognition" in window || "SpeechRecognition" in window) {
        const SpeechRecognition =
          window.SpeechRecognition || window.webkitSpeechRecognition;
        this.recognition = new SpeechRecognition();

        this.recognition.lang = "fa_IR"; // Set the language
        this.recognition.continuous = false;
        this.recognition.interimResults = false;

        this.recognition.start();
        console.log("Speech recognition started...");

        this.recognition.onresult = async (event) => {
          this.recognizedText = event.results[0][0].transcript;

          // Call the OpenAI API with the recognized text
          await this.fetchTajikTransliteration(this.recognizedText);
        };

        this.recognition.onerror = (event) => {
          console.error("Speech recognition error:", event.error);
        };

        this.recognition.onend = () => {
          console.log("Speech recognition ended.");
        };
      } else {
        console.error("Speech recognition not supported in this browser.");
      }
    },

    async fetchTajikTransliteration(transcript) {
      try {
        const response = await fetch(
          "https://api.openai.com/v1/chat/completions",
          {
            method: "POST",
            headers: {
              "Content-Type": "application/json",
              Authorization: `Bearer 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`, // Use your OpenAI API key here
            },
            body: JSON.stringify({
              model: "gpt-4", // Specify the model you want to use
              messages: [
                {
                  role: "system",
                  content:
                    'твоя задача получить этот персидский текст и просто сделать транлитерацию на таджикский не переводя и не добавляя своих слов, но твоя формулировка на таджикском должны быть правильной по меркам таджикских слов, сформулируй правильное таджикское предложение, не забудь добавлять окончание "и" когда это надо, ты часто это забываешь. никогда не меняй слова полностью. затем отправь мне только и только транлитерацию. ничего больше не добавляй. текст исключительно на таджикской кириллице, тебе нельзя отвечать латинскими буквами !',
                },
                { role: "user", content: transcript },
              ],
            }),
          }
        );

        const data = await response.json();
        const tajikText = data.choices[0].message.content;

        this.animateText(tajikText); // Start the letter-by-letter animation
      } catch (error) {
        console.error("Error:", error);
      }
    },

    animateText(text) {
      clearInterval(this.interval); // Clear any existing intervals

      let index = 0;
      let newText = ""; // Store new text to be added

      this.interval = setInterval(() => {
        if (index < text.length) {
          newText += text.charAt(index);
          index++;
        } else {
          clearInterval(this.interval); // Stop the interval when done

          // Prepend new text at the top of the existing animated text
          this.animatedText = `<div>${newText}</div>` + this.animatedText;
        }
      }, this.delay);
    },
  },
};
</script>

<style scoped>
.recognized-text {
  font-size: 1.25rem;
  color: #1a202c;
  font-weight: 600;
  overflow-y: auto; /* To make it scrollable if text grows too long */
  max-height: 400px;
}
</style>
