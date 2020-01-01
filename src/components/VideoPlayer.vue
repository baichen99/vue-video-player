<template>
  <div class="container">
        <div>
            <video @keyup.space="togglePlay" ref="vvideo" class="vvideo">
                <source :src="src" type="video/mp4">
            </video>
        </div>

        <div id="vinfo">
            <button @click="pause" v-if="videoInfo.playState">暂停</button>
            <button @click="play" v-if="!videoInfo.playState">播放</button>
        </div>
        <span>{{videoInfo.currentTime }}/{{ videoInfo.duration }}</span>
        <div class="progressBar" ref="progressBar">
            <div class="progress" ref="progress"></div>
        </div>
    </div>
</template>

<script>
export default {
    props: ['src'],
    data() {
        return {
            videoDom: null,
            progressBarDom: null,
            progressDom: null,
            videoInfo: {
                playState: false,
                currentTime: 0,
                duration: 0,
                percent: 0

            }
        }
    },

    methods: {
        play(e) {
            this.videoInfo.playState = true;
            this.videoDom.play();
        },
        pause(e) {
            this.videoInfo.playState = false;
            this.videoDom.pause();
        },
        togglePlay(e){
            console.log(1)
            if(this.videoInfo.playState == false) this.play();
            else this.pause();
        },
        initProgress() {
            this.progressBarDom.addEventListener('mousedown', this.mouseDown);
        },
        initMetaData() {
            this.videoDom.addEventListener('loadedmetadata', () => {
                this.videoInfo.duration = this.videoDom.duration
            })
            this.videoDom.addEventListener('timeupdate', () => {
                this.progressDom.style.width = this.videoDom.currentTime / this.videoInfo.duration * 100 + '%';
                this.videoInfo.currentTime = this.videoDom.currentTime;
            })
        },
        mouseDown(e) {
            this.percent = e.offsetX / this.progressBarDom.clientWidth * 100;
            this.progressDom.style.width = this.percent + '%';
            this.progressBarDom.addEventListener('mousemove', this.mouseDownMove);
            this.progressBarDom.addEventListener('mouseup', this.mouseDownMoveUp);
        },
        mouseDownMove(e) {
            this.percent = e.offsetX / this.progressBarDom.clientWidth * 100;
            this.progressDom.style.width = this.percent + '%';    
        },
        mouseDownMoveUp(e) {
            this.progressBarDom.removeEventListener('mousemove', this.mouseDownMove);
            this.videoDom.currentTime = this.percent / 100 * this.videoInfo.duration;
        }
    },

    mounted() {
        this.videoDom = this.$refs.vvideo;
        this.progressBarDom = this.$refs.progressBar;
        this.progressDom = this.$refs.progress;
        this.initProgress();
        this.initMetaData();
    }
}
</script>

<style lang="scss" scoped>
.container {
    width: 500px;
    .vvideo {
        width: 100%;
    }
    .progressBar {
        width: 100%;
        height: 20px;
        background-color: #f9e1e3;
        border-radius: 3px;
        cursor: pointer;
        .progress {
            width: 0%;
            height: 100%;
            background-color: #e46a70;
            border-radius: 3px;
        }
    }
}
</style>
