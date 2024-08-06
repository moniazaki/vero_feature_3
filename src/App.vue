<template>
  <div class="container">
    <video
      v-if="renderVideo"
      ref="videoPlayer"
      controls
      @timeupdate="checkQuiz"
      
    >
      <source src="../public/video/video.mp4" type="video/mp4" />
      Your browser does not support the video tag.
    </video>
    
    <div v-if="showQuizOverlay" class="quiz-overlay">
      <div class="title">
        <p>Time to test what you learned</p>
      </div>  
        <p>{{ currentQuestion.question }}</p>
      <div class="subtitle">
          <p >Select all those you think are correct</p>
      </div>
      
      <form @submit.prevent="submitAnswer">
        <div class="quiz-options">
          <div v-for="(option, index) in currentQuestion.options" :key="index" class="quiz-option">
            <label :class="{ selected: selectedOption === index }" @click="selectOption(index)">
              {{ option }}
            </label>
          </div>
        </div>
        <button type="submit" class="submit-btn">Submit</button>
      </form>
    </div>
    
  </div>
</template>



<script>
export default {
  data() {
    return {
      videoPath: '', 
      quizData: [
        {
          id: 1,
          seconds: 1,
          question: 'What is 2 + 2?',
          options: ['3', '4', '5','6','7'],
          correct_answer: 1, 
        },
        {
          id: 2,
          seconds: 7,
          question: 'What is the capital of France?',
          options: ['Berlin', 'London', 'Paris','Prague','Rome'],
          correct_answer: 2, 
        },
      ],
      currentQuizIndex: 0,
      showQuizOverlay: false,
      currentQuestion: {},
      selectedOption: null,
      renderVideo: false,
      seeking: false,
      skipToTime: null,
    };
  },
  mounted() {
    this.loadVideoData();
  },
  methods: {
    loadVideoData() {
      this.videoPath = "../public/video/video.mp4"
      this.renderVideo = true;
    },
    checkQuiz() {
      const videoPlayer = this.$refs.videoPlayer;
      const currentTime = Math.floor(videoPlayer.currentTime);

      if (this.seeking) return; 
      if (this.currentQuizIndex < this.quizData.length && currentTime === this.quizData[this.currentQuizIndex].seconds) {
        videoPlayer.pause();
        this.showQuizOverlay = true;
        this.currentQuestion = this.quizData[this.currentQuizIndex];
      }
    },
   
    submitAnswer() {
      const question = this.currentQuestion;
      const answer = this.selectedOption;

      console.log('User selected:', question.options[answer]);

      this.showQuizOverlay = false;
      this.currentQuizIndex++;
      this.selectedOption = null;

      this.$refs.videoPlayer.play();
    },
    selectOption(index) {
      this.selectedOption = index;
    },
  },
  
};
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: #ffffff;
  color: #000000;
  padding: 20px;
  border-radius: 10px;
  position: relative;
}

video {
  border-radius: 10px;
  width:80%;
  height: auto;
}

.quiz-overlay {
  position: absolute;
  top: 60%;
  left: 50%;
  transform: translate(-50%, -60%);
  background-color: rgba(0, 0, 0, 0.61);
  color: white;
  padding: 20px;
  border:none;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  z-index: 1000;
  width: 75%;
  /* max-width: 500px; */
  height: 60%;
  text-align: start;
  border-radius: 15px;
}

.title{
  font-size:20px;
  margin-bottom: 35px;

}
.subtitle{
  font-size: 12px;
  margin-bottom: 10px;

}
.quiz-overlay p {
  font-size: 1.2em;
  margin-bottom: 15px;
}

.quiz-options {
  display: flex;
  flex-wrap: wrap;
}

.quiz-option {
  margin: 10px;
}

.quiz-option label {
  display: flex;
  align-items: center;
  justify-content: start;
  background-color: rgba(0, 0, 0, 0.61);
  color: white;
  padding: 10px;
  border-radius: 5px;
  cursor: pointer;
  width: 300px;
  margin-right: 20px;
  border: 2px solid white;
  transition: border-color 0.3s;
}

.quiz-option label.selected {
  border-color: #ff3366;
}

.quiz-option input[type="checkbox"] {
  display: none;
}

.submit-btn {
  background-color: #ff3366;
  color: white;
  border: none;
  padding: 10px 40px;
  cursor: pointer;
  border-radius: 25px;
  margin-top: 20px;
  transition: background-color 0.3s;
  position: absolute;
  bottom: 20px;
  right: 20px;
}

.submit-btn:hover {
  background-color: #e02f5f;
}


</style>


