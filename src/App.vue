<template>
  <div class="container">
    <header><div class="logo"></div></header>

    <h2 class="header">
      Як заробляти <span class="orange">від $100</span> на день на налаштуванні
      рекламних кампаній Facebook+Instagram
    </h2>
    <div class="lesson-title">
      <h3 class="title">{{ title }}</h3>
      <p class="subtitle">{{ subtitle }}</p>
    </div>
    <div class="lesson-block">
      <div class="youtube-area">
        <YouTube
          class="lesson-video"
          :src="videoUrl"
          :autoplay="1"
          @state-change="
            this.$refs.youtube.getPlayerState() === 1
              ? startTimer()
              : stopTimer()
          "
          ref="youtube"
        />
      </div>
      <div class="lessons-list">
        <ul v-if="posts && posts.length">
          <li
            v-for="(post, index) of posts"
            class="lesson-link"
            :id="index"
            :class="
              index === currentElemnt ? 'lesson-link-active' : 'lesson-link'
            "
            @click="counter > index ? changeLesson(index) : false"
            :key="post"
            :ref="index"
          >
            <p class="background-image"></p>
            <p class="lesson-link-text">{{ post.subtitle }}</p>
          </li>
        </ul>

        <ul v-if="errors && errors.length">
          <li v-for="(index, error) of errors" :key="index">
            {{ error.message }}
          </li>
        </ul>
      </div>
    </div>
    <div class="bottom-block">
      <div class="lesson-block-description">
        <h4 class="lesson-description-header">
          У цьому епізоді ви дізнаєтесь:
        </h4>
        <p class="lesson-description">{{ description }}</p>
      </div>
      <div class="button-block">
        <p
          v-if="
            currentElemnt !== posts.length - 1 && counter >= currentElemnt + 1
          "
          class="button-description"
        >
          Вже переглянули? Отримайте доступ до наступного:
        </p>
        <button
          v-if="
            currentElemnt !== posts.length - 1 && counter >= currentElemnt + 1
          "
          class="next-lesson-button"
          @click="
            currentElemnt < posts.length - 1
              ? changeLesson(currentElemnt + 1)
              : false
          "
        >
          Наступний епізод
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import YouTube from "vue3-youtube";

export default {
  name: "Default-layout",

  data: () => ({
    posts: [],
    errors: [],
    videoUrl: "",
    title: "",
    subtitle: "",
    description: "",
    currentElemnt: "",
    currentTime: "",
    currentStatus: "",
    timer: null,
    disabled: [],
    counter: 0,
  }),

  components: { YouTube },

  created() {
    axios
      .get(`./db.json`)
      .then((response) => {
        this.posts = response.data;
      })
      .catch((e) => {
        this.erros.push(e);
      });
  },

  updated() {
    if (!this.videoUrl) {
      this.changeLesson(0);
    }
  },

  methods: {
    changeLesson(index) {
      this.videoUrl = this.posts[index].video_url;
      this.title = this.posts[index].title;
      this.description = this.posts[index].description;
      this.subtitle = this.posts[index].subtitle;
      this.currentElemnt = index;
      this.currentTime = this.posts[index].video_time;
    },

    startTimer() {
      if (this.currentTime !== 0) {
        this.timer = setInterval(() => {
          this.currentTime--;
          console.log(this.currentTime);
        }, 1000);
      }
    },

    stopTimer() {
      clearTimeout(this.timer);
      this.timer = null;
    },
  },

  watch: {
    currentTime(time) {
      if (time <= 0) {
        this.stopTimer();
        if (this.counter >= this.currentElemnt) {
          this.counter += 1;
          console.log(this.counter);
        }
      }
    },
  },
};
</script>

<style scoped>
.container {
  width: 1200px;
  margin: auto;
}

.logo {
  background-image: url(./i/atom_logo.svg);
  width: 100px;
  height: 75px;
  background-size: contain;
  background-repeat: no-repeat;
  margin: auto;
}

.header {
  max-width: 75%;
  margin: auto;
  text-align: center;
}
.orange {
  color: black;
}

.title {
  color: orange;
}

.subtitle {
  margin-left: 5px;
}

.youtube-area {
  padding: 0 30px 50px 0;
}

.lesson-title {
  display: flex;
  justify-content: center;
  align-items: center;
  margin: auto;
  margin-top: 50px;
  max-width: 85%;
}

.lesson-block {
  max-width: 85%;
  margin: auto;
  display: flex;
  margin-top: 60px;
}

.background-image {
  background-image: url(https://i.ytimg.com/vi/7b0maNVkwCE/hqdefault.jpg?s…AFwAcABBg==&rs=AOn4CLDaIy41J7S3zs1H-M5-86B594Eu5A);
  margin: 0;
  height: 64px;
  width: 100px;
  background-size: cover;
  background-repeat: no-repeat;
}

.lessons-list {
  width: 100%;
  display: flex;
  align-items: baseline;
  justify-content: end;
}

.lesson-link-active {
  background: orange !important;
}

.lesson-link {
  width: 400px;
  height: 64px;
  background: rgb(128, 128, 128, 0.5);
  display: flex;
  margin-bottom: 10px;
  cursor: pointer;
}

.lesson-link a > p {
  margin: 0;
}

.disabled {
  pointer-events: none;
  background-color: rgb(51, 51, 51, 0.7);
}

.lesson-link-text {
  text-decoration: none;
  color: whitesmoke;
  display: flex;
  flex-direction: column;
  justify-content: center;
  width: 200px;
  text-align: center;
  margin-left: 35px;
}

.lesson-block-description {
  margin-top: 50px;
  width: 75%;
  margin: auto;
  text-align: left;
}

.bottom-block {
  max-width: 85%;
  display: flex;
  margin: auto;
  margin-top: 50px;
  justify-content: space-between;
}

.lesson-description {
  width: 50%;
}

.lesson-description-header {
  margin-top: 0;
  color: orange;
  font-size: 30px;
}

.button-description {
  margin: 0;
}

.button-block {
  width: 30%;
}

.next-lesson-button {
  padding: 0;
  border: none;
  background-color: orange;
  height: 60px;
  width: 250px;
  color: white;
  margin-top: 30px;
}

footer {
  height: 70px;
}

.content {
}

ul {
  list-style: none;
  margin: 0;
  padding: 0px;
}

li {
  margin: 0 5px;
}

.link {
  text-decoration: none;
}
</style>
