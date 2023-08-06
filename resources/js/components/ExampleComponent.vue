<template>
  <div class="box">
    <div class="swiper-container" ref="swiperContainer">
      <div class="swiper-wrapper">
        <div
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
          ></video>
          <div class="video-controls">
            <div class="custom-button play-button1" @click="pause(index + 1)">
              {{ videoActive ? "⏸" : "▶" }}
            </div>
            <div
              id="sound-button"
              class="custom-button volume-button1"
              @click="mute(index + 1)"
            >
              🔊
            </div>
          </div>
        </div>
        <!-- Add more video items as needed -->
      </div>
    </div>
  </div>
</template>

<script>
import Swiper from "swiper";
import scrollama from "scrollama";
export default {
  data() {
    return {
      videoActive: false,
      listMovies: [
        {
          id: "1",
          title: "movie5.mp4",
        },
        {
          id: "2",
          title: "movie6.mp4",
        },
        {
          id: "3",
          title: "movie4.mp4",
        },
        {
          id: "4",
          title: "movie2.mp4",
        },
      ],
    };
  },
  mounted() {
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
          // Bật âm thanh và phát video
          //   videoElement.muted = false;
          //   videoElement.play().catch((error) => {
          // Xử lý lỗi nếu tự động phát bị chặn bởi trình duyệt
          // console.log(error);
          //   });
        } else {
          // Dừng video nếu video ra khỏi khung nhìn
          console.log("down");
          videoElement.pause();
        }

        this.videoActive = true;
      });
    }, options);

    videoItems.forEach((videoItem) => {
      observer.observe(videoItem);
    });
  },

  methods: {
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
        } else {
          // Nếu video đang chạy, thì dừng video
          videoItem.pause();
          this.videoActive = false;
        }
      }
    },
    mute(index) {
      const videoItem = document.querySelector(`#video-${index}`);
      if (videoItem) {
        videoItem.muted = !videoItem.muted;
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
  /* Add relative positioning for the video container */
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
  /* Customize the maximum width of the video */
  height: 500px;
  object-fit: cover;
}

/* Custom control buttons */
.video-controls {
  position: absolute;
  bottom: 20px;
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
</style>
