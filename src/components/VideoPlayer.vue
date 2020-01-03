<template>
  <div class="container" ref="container" @keyup.space="togglePlay">
    <div class="video">
        <div>
            <video ref="vvideo" class="vvideo">
                <source :src="src" type="video/mp4">
            </video>
        </div>

        <div id="vinfo">
            <span class="el-icon-refresh" @click="replay"></span>
            <span class="el-icon-video-pause" @click="pause" v-if="videoInfo.playState"></span>
            <span class="el-icon-video-play" @click="play" v-if="!videoInfo.playState"></span>
            <span class="el-icon-circle-plus-outline" @click="play"></span>
            <el-input-number v-model="videoInfo.playbackRate" :min="0.2" :max="2" :step="0.2"></el-input-number>
        </div>
        <span>{{videoInfo.currentTime }}/{{ videoInfo.duration }}</span>
        <div class="progressBar" ref="progressBar">
            <div class="progress" ref="progress"></div>
        </div>
    </div>

    <div class="panel">
        <span :class="t.hightlight" v-for="t in textData" :key="t.value">{{ t.value }}</span>
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
            containerDom: null,
            videoInfo: {
                isDrag: false,
                playState: false,
                currentTime: 0,
                duration: 0,
                playbackRate: 1,

            },
            textData: [
                {
                    start: 0,
                    end: 1.2,
                    value: "hello",
                },
                {
                    start: 1.2,
                    end: 2.4,
                    value: "world",
                },
                {
                    start: 2.0,
                    end: 3.1,
                    value: "I",
                },
                {
                    start: 3.1,
                    end: 5.0,
                    value: "am",
                },
                {
                    start: 5.0,
                    end: 6.0,
                    value: "baichen",
                },
                
            ]
        }
    },

    methods: {
        play(e) {
            this.videoInfo.playState = true;
        },
        pause(e) {
            this.videoInfo.playState = false;
        },
        replay() {
            this.videoDom.currentTime = 0;
        },
        togglePlay(e){
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
                if(!this.isDrag)
                    this.progressDom.style.width = this.videoDom.currentTime / this.videoInfo.duration * 100 + '%';
                this.videoInfo.currentTime = this.videoDom.currentTime;
                this.hightlightText()
            })
        },
        mouseDown(e) {
            let percent = e.offsetX / this.progressBarDom.clientWidth * 100;
            this.progressDom.style.width = this.percent + '%';
            this.progressBarDom.addEventListener('mousemove', this.mouseDownMove);
            this.progressBarDom.addEventListener('mouseup', this.mouseDownMoveUp);
            this.isDrag = true;
        },
        mouseDownMove(e) {
            let percent = e.offsetX / this.progressBarDom.clientWidth * 100;
            this.progressDom.style.width = percent + '%';    
        },
        mouseDownMoveUp(e) {
            let percent = e.offsetX / this.progressBarDom.clientWidth;
            this.progressBarDom.removeEventListener('mousemove', this.mouseDownMove);
            this.videoDom.currentTime = percent  * this.videoInfo.duration;
            this.isDrag = false;
        },
        hightlightText() {
            this.textData.forEach((item,index)=>{
                if(this.videoInfo.currentTime > item.start && this.videoInfo.currentTime < item.end)
                    item.hightlight = "highlight";
                else {
                    item.hightlight = ""
                }
            })
        }
    },

    watch: {
        'videoInfo.playbackRate': function(newV, oldV) {
            this.videoDom.playbackRate = newV;
        },
        'videoInfo.playState': function(newV, oldV) {
            if(newV == true)
                this.videoDom.play()
            else 
                this.videoDom.pause()
        }
    },

    mounted() {
        this.videoDom = this.$refs.vvideo;
        this.progressBarDom = this.$refs.progressBar;
        this.progressDom = this.$refs.progress;
        this.containerDom = this.$refs.container;
        this.initProgress();
        this.initMetaData();
    }
}
</script>

<style lang="scss" scoped>
.container {
    width: 90%;
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    .vvideo {
        width: 100%;
    }
    #vinfo {
        display: flex;
        justify-content: center;
        align-items: center;
        .el-icon-video-pause, .el-icon-video-play, .el-icon-circle-plus-outline, .el-icon-refresh {
            margin: 10px;
            cursor: pointer;
            font-size: 50px;
        }
    }
    .progressBar {
        width: 100%;
        height: 80px;
        background-color: #f9e1e3;
        border-radius: 3px;
        cursor: pointer;
        .progress {
            width: 0%;
            height: 100%;
            background-color: #e46a70;
            border-radius: 3px;
            border-right: 1px solid #e6e6e6;
        }
        .slider-button {
            height: 1rem;
            width: 1rem;
            border-radius: 50%;
            background-color: aqua;
            position: relative;
            left: -0.5rem;
            top: -1rem;
        }
    }
    .panel {
        width: 300px;
        height: 300px;
        padding: 10px;
        border: 1px solid black;
        span {
            margin: 2px;
        }
        .highlight {
            border: 2px solid gold;
        }
    }
}
</style>
