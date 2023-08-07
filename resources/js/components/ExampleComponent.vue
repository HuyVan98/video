<template>
  <div>
    <top-app></top-app>
    <div class="box">
      <div class="swiper-container" ref="swiperContainer">
        <div class="swiper-wrapper">
          <div
            id="swiper-slide-custom"
            class="swiper-slide"
            v-for="(movie, index) in listMovies"
            :key="index"
          >
            <video
              :id="'video-' + (index + 1)"
              :class="['video-item']"
              autoplay
              muted
              :src="movie.title"
              @ended="videoEnded(index + 1)"
              @timeupdate="updateTime(index + 1)"
              @play="videoStarted(index + 1, movie)"
              @click="pause(index + 1)"
            ></video>

            <div v-if="!videoActive" class="video-controls">
              <div class="custom-button play-button1" @click="pause(index + 1)">
                ▶
              </div>
            </div>

             <div class="video-time">{{ formattedTime }}</div>
            <div class="title-thum" @click="mute(index + 1)">{{ muteActive ? "🔊" : "🔇" }}</div>
            <div class="controls">
              <div class="progress">
                <div :id="'progress__filled-' + (index + 1)" class="progress__filled"></div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="title-video">
        <div>
          <input class="avata" type="image" :src="videoData.image" alt="" />
        </div>
        <div class="title">{{ videoData.name }}</div>
      </div>
      <div class="decr-video">
        <span>{{ videoData.text }}</span>
      </div>
      <div class="button-tbn">
        <button class="btn">+ Theo dõi</button>
      </div>
    </div>
    <footer-app></footer-app>
  </div>
</template>

<script>
import Swiper from "swiper";

export default {
  data() {
    return {
      videoActive: false,
      muteActive: false,
      currentTime: 0,
      formattedTime: "00:00",
      listMovies: [
        {
          id: "1",
          title: "movie5.mp4",
          avata: "Lưu Diệc Phi (Cô Cô)",
          image: "./luudiecphi.png",
          text: "Video 1 đã bắt đầu phát",
        },
        {
          id: "2",
          title: "movie6.mp4",
          avata: "Cô Cô",
          image: "./coco.png",
          text: "Video 2 đã bắt đầu phát",
        },
        {
          id: "3",
          title: "movie4.mp4",
          avata: "Chí Bình Đại Ca",
          image: "./chibinh.png",
          text: "Video 3 đã bắt đầu phát",
        },
        {
          id: "4",
          title: "movie2.mp4",
          avata: "Lưu Diệc Phi (Cô Cô)",
          image: "./luudiecphi.png",
          text: "Video 4 đã bắt đầu phát",
        },
      ],
      videoData: {
        name: "",
        image: "",
        text: "",
      },
    };
  },
  mounted() {
    const firstMovie = this.listMovies[0];
    this.videoData.name = firstMovie.avata;
    this.videoData.image = firstMovie.image;
    this.videoData.text = firstMovie.text;
    // JavaScript code for handling swipe gestures with Swiper.js
    new Swiper(".swiper-container", {
      direction: "vertical", // Set the direction to 'vertical' for vertical layout
      grabCursor: true,
      loop: false,
      autoplay: {
        delay: 5000, // Độ trễ giữa các slide (ms)
      },
      navigation: {
        nextEl: ".swiper-button-next",
        prevEl: ".swiper-button-prev",
      },
      on: {
        init: function () {
          // Mở tiếng và tự động phát video khi trình duyệt đã tải và hiển thị slide video đầu tiên
          const currentSlide = this.slides[this.activeIndex];
          const videoElement = currentSlide.querySelector("video");
          if (videoElement) {
            videoElement.muted = false;
            videoElement.play().catch((error) => {
              // Xử lý lỗi nếu tự động phát bị chặn bởi trình duyệt
              console.log(error);
            });
          }
        },
        slideChangeTransitionStart: function () {
          // Mute tất cả video khi chuyển đổi slide
          this.slides.forEach((slide) => {
            const videoElement = slide.querySelector("video");
            if (videoElement) {
              videoElement.muted = true;
            }
          });

          // Mở tiếng và tự động phát video khi chuyển đến slide mới
          const currentSlide = this.slides[this.activeIndex];
          const videoElement = currentSlide.querySelector("video");
          if (videoElement) {
            videoElement.muted = false;
            videoElement.play().catch((error) => {
              // Xử lý lỗi nếu tự động phát bị chặn bởi trình duyệt
              console.log(789);
            });
          }
        },
      },
    });

    // Lắng nghe sự kiện cuộn video để tắt video khác

    const videoItems = document.querySelectorAll(".video-item");
    const options = {
      threshold: 0.5, // Threshold 0.5 đại diện cho việc gần 50% phần tử hiển thị trong khung nhìn
    };

    const observer = new IntersectionObserver((entries) => {
      entries.forEach((entry) => {
        const videoElement = entry.target;

        // Kiểm tra nếu video xuất hiện trong khung nhìn đủ lớn (đạt ngưỡng threshold)
        if (entry.isIntersecting) {
          console.log("up");
        } else {
          // Dừng video nếu video ra khỏi khung nhìn
          console.log("down");
          videoElement.pause();
        }

        this.videoActive = true;
        this.muteActive = true;
      });
    }, options);

    videoItems.forEach((videoItem) => {
      observer.observe(videoItem);
    });
  },

  methods: {
    videoClicked(index) {
      // Xử lý sự kiện click trên video tại index đã cho, sử dụng thông tin từ đối tượng movie
      console.log("Đã click");
    },
    handleProgress(video, index) {
      const progressBar = document.getElementById(`progress__filled-${index}`);
      const progressPercentage = (video.currentTime / video.duration) * 100;
      progressBar.style.flexBasis = `${progressPercentage}%`;
    },
    videoStarted(index, video) {
      // Xử lý khi video bắt đầu phát
      console.log(`Video ${index} đã bắt đầu phát`);

      this.videoData.name = video.avata;
      this.videoData.image = video.image;
      this.videoData.text = video.text;
    },
    updateTime(index) {
      // Cập nhật thời gian hiện tại của video
      const videoItem = document.querySelector(`#video-${index}`);
      if (videoItem) {
        this.currentTime = videoItem.currentTime;
        this.formattedTime = this.formatTime(this.currentTime);
      }
      this.handleProgress(videoItem, index)
    },
    formatTime(time) {
      // Hàm định dạng thời gian từ giây sang định dạng mm:ss
      const minutes = Math.floor(time / 60);
      const seconds = Math.floor(time % 60);
      return `${String(minutes).padStart(2, "0")}:${String(seconds).padStart(
        2,
        "0"
      )}`;
    },
    isVideoPlaying(index) {
      // Kiểm tra xem video có đang chạy hay không dựa vào trạng thái của video
      const videoItem = document.querySelector(`#video-${index}`);
      return videoItem ? !videoItem.paused : false;
    },
    isVideoPlaying(index) {
      // Kiểm tra xem video có đang chạy hay không dựa vào trạng thái của video
      const videoItem = document.querySelector(`#video-${index}`);
      return videoItem ? !videoItem.paused : false;
    },
    pause(index) {
      const videoItem = document.querySelector(`#video-${index}`);
      if (videoItem) {
        if (videoItem.paused) {
          // Nếu video đang pause, thì chạy video
          videoItem.play();
          this.videoActive = true;
          this.muteActive = true;
        } else {
          // Nếu video đang chạy, thì dừng video
          videoItem.pause();
          this.videoActive = false;
          this.muteActive = false;
        }
      }
    },
    mute(index) {
      const videoItem = document.querySelector(`#video-${index}`);
      if (videoItem) {
        videoItem.muted = !videoItem.muted;
        this.muteActive = !this.muteActive;
      }
    },
    videoEnded(index) {
      // Xử lý khi video được phát xong
      console.log(`Video ${index} đã kết thúc`);
      // Chuyển tới slide tiếp theo nếu video không phải là slide cuối cùng
      if (this.$refs.swiperContainer && this.$refs.swiperContainer.swiper) {
        const swiperInstance = this.$refs.swiperContainer.swiper;
        if (swiperInstance.activeIndex !== swiperInstance.slides.length - 1) {
          swiperInstance.slideNext();
          this.videoActive = true;
        }
      }
    },
  },
};
</script>

<style>
body {
  margin: 0;
  padding: 0;
  font-family: system-ui;
  background: black;
}
.btn {
  width: 80px;
  height: 30px;
  background: #2828cb;
  color: #fff;
  border: none;
  border-radius: 4%;
}
.button-tbn {
  left: 185px;
  position: absolute;
  color: white;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 13px;
  top: 681px;
  display: flex;
  align-items: center;
}
.decr-video {
  left: 2px;
  position: absolute;
  color: white;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 13px;
  top: 725px;
  display: flex;
  align-items: center;
}
.avata {
  width: 30px;
  border-radius: 100%;
  margin-right: 10px;
}
.title-video {
  left: 2px;
  position: absolute;
  color: white;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 13px;
  top: 680px;
  display: flex;
  align-items: center;
}
.title-thum {
  left: 345px;
  top: 0px;
  position: absolute;
  color: white;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 20px;
}
.right .content {
  position: relative; /* Cần thiết để vị trí tương đối với đường thẳng */
}

.right .content::after {
  content: "";
  position: absolute;
  bottom: -5px; /* Cách 5px với nội dung */
  left: 0;
  width: 100%;
  height: 1px;
  background-color: #fff;
}

.top {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  background-color: #181818;
  color: white;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  padding: 10px;
  height: 50px;
}

.left,
.right {
  padding: 10px;
}

.left {
  width: 50%;
  text-align: right;
}
.right-content {
  width: 70%;
}

.notification-icon {
  width: 30%;
  text-align: center;
  font-size: 24px;
}
.right {
  text-align: left;
  display: flex;

  flex-direction: row;
  align-items: center;
  width: 50%;

  justify-content: space-between;
}

.footer {
  position: fixed;
  left: 0;
  bottom: 0;
  width: 100%;
  background-color: #181818;
  color: #f0f0f9;
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 10px; /* Thay thế padding: 10px; */
  height: 71px;
}

.footer > div {
  flex: 1;
  text-align: center;
}
.icon {
  font-size: 25px;
}
.footer span {
  display: block;
  margin: 0;
  padding: 5px;
}

.box {
  display: flex;
  justify-content: center;
  /* căn giữa theo chiều ngang */
  align-items: center;
  /* căn giữa theo chiều dọc */
  height: 100vh;
}
#swiper-slide-custom {
  display: flex;
  justify-content: center;
  align-items: center;
  background: black;
}
.video-time {
  position: absolute;
  bottom: 10px;
  left: 335px;
  background-color: rgba(0, 0, 0, 0.5);
  color: white;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 14px;
}
.box {
  display: flex;
  justify-content: center; /* căn giữa theo chiều ngang */
  align-items: center; /* căn giữa theo chiều dọc */
  height: 100vh;
}
/* CSS styles for the video container */
.swiper-container {
  width: 100%;
  height: 500px;
  overflow: hidden;
  position: relative;
}

.swiper-slide {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.video-item {
  width: 100%;
  max-width: 800px;
  height: 500px;
  object-fit: cover;
}

/* Custom control buttons */
.video-controls {
  position: absolute;
  bottom: 50%;
  left: 50%;
  transform: translateX(-50%);
  background-color: rgba(0, 0, 0, 0.5);
  padding: 10px;
  border-radius: 10px;
  display: flex;
}

.custom-button {
  color: white;
  font-size: 20px;
  margin: 0 5px;
  cursor: pointer;
}

/* Hide default video controls */
video::-webkit-media-controls {
  /* Safari, Chrome, and other webkit browsers */
  display: none;
}

video::-webkit-media-controls-start-playback-button {
  /* Safari only */
  display: none;
}

video::-webkit-media-controls-enclosure {
  /* Safari only */
  display: none;
}

video::-webkit-media-controls-panel {
  /* Safari only */
  display: none;
}

/* line time video */

.video {
  width: 100%;
}
.controls {
  display: flex;
  position: absolute;
  bottom: 0;
  width: 100%;
  flex-wrap: wrap;
  background: rgba(0, 0, 0, 0.1);
  transform: translateY(0);
}
.progress {
  flex: 10;
  position: relative;
  display: flex;
  flex-basis: 100%;
  height: 5px;
  /* background: rgba(0,0,0,0.5); */
  background: rgba(255, 255, 255, 0.5);
  cursor: pointer;
  height: 2px;
}
.progress__filled {
  width: 50%;
  background: #fd0045;
  flex: 0;
  flex-basis: 0%;
}
</style>
