<template>
  <div id="app-items" style="text-align: center">
    <div v-show="header" style="height: auto;">
      <q-btn
        :flat="$props.buttonsStyle === 'flat'"
        :push="$props.buttonsStyle === 'push'"
        :outline="$props.buttonsStyle === 'outline'"
        :glossy="$props.buttonsStyle === 'glossy'"
        :round="$props.buttonsType === 'round'"
        :rounded="$props.buttonsType === 'rounded'"
        :color="$props.buttonColor"
        v-show="inputStarted"
        @click="takePictured()"
      >
          <q-icon :size="this.QIconTab" name="camera_alt" />
      </q-btn>
      <input v-show="inputFile" type="file" accept="image/*;capture=camera" />
    </div>
      <div
        class="photo relative-position"
        :class="{'photo-tab': activePhotoTab}"
        v-show="takePhoto"
      >
        <div
          v-show="camera"
          class="camera animate-fade"
          :style="{width: transformWidth($props.width) + 'px'}"
        >
          <video ref="video"
            :style="{height: transformHeight($props.height) + 'px'}"
            :class="{flipped: activeFlipped, 'video-tab': activeVideoTab}"
            class="video"
            autoplay
          >
          </video>
          <q-btn
            @click="snapshot()"
            round
            class="absolute btn-camera btn-center"
            :class="{
            'btn-left': activeBtnSnapLeft,
            'btn-left-tab': activeBtnSnapTabLeft,
            'btn-left-tab-big': activeBtnSnapTabLeftBig,
            'btn-camera-tab': activeBtnCameraTab
            }"
            outline
            color="white"
          >
            <q-icon :size="this.QIconTab" color="white" name="add_a_photo"></q-icon>
          </q-btn>
          <div class="btnsFacingMode" v-show="groupFacingMode" >
            <q-btn
              v-if="facingMode"
              @click="changeCamera()"
              round
              class="absolute btn-camera"
              :class="{'btn-camera-tab': activeBtnCameraTab}"
              outline
              color="white"
            >
              <q-icon :size="this.QIconTab" color="white" name="camera_front"></q-icon>
            </q-btn>
            <q-btn
              v-else
              @click="changeCamera()"
              round
              class="absolute btn-camera"
              :class="{'btn-camera-tab': activeBtnCameraTab}"
              outline
              color="white"
            >
              <q-icon :size="this.QIconTab" color="white" name="camera_rear"></q-icon>
            </q-btn>
          </div>
          <q-inner-loading color="primary" size="30%" :visible="visibleLoad" />
        </div>

        <div
          v-show="picture"
          :class="{'resultPicture-tab': activeResultPictureTab}"
          class="animate-fade"
          id="resultPicture"
          :style="{width: transformWidth($props.width) + 'px'}"
        >
          <canvas
            :class="{'canvas-tab': activeCanvasTab}"
            ref="canvas"
            :style="{height: transformHeight($props.height) + 'px', transform: 'rotateY(' + this.rotate + ')'}"
          ></canvas>
          <q-btn
            @click="saved()"
            round
            outline
            class="absolute btn-camera"
            style="transform: rotateY('0deg')"
            :class="{
            'btn-left': activeBtnSaveLeft,
            'btn-left-tab': activeBtnSnapTabLeft,
            'btn-left-tab-big': activeBtnSnapTabLeftBig,
            'btn-camera-tab': activeBtnCameraTab
            }"
            color="positive"
          >
            <q-icon :size="this.QIconTab" color="positive" name="cloud_done" />
          </q-btn>
          <q-btn
            @click="takePictured()"
            round
            outline
            class="absolute btn-camera"
            style="transform: rotateY('0deg')"
            :class="{'btn-camera-tab': activeBtnCameraTab}"
            color="positive"
          >
            <q-icon :size="this.QIconTab" color="positive" name="power_settings_new"></q-icon>
          </q-btn>
        </div>
      </div>


  </div>

</template>
<script>

import {
  AppFullscreen,
  QIcon,
  QBtn,
  QVideo,
  Toast,
  Dialog,
  QTransition,
  Platform,
  QSpinnerGears,
  QInnerLoading
} from 'quasar';

import 'quasar-extras/animate';

export default {
  name: 's-take-pictured',
  props:{
    buttonsStyle: {
      type: String,
      default: 'flat'
    },
    buttonsType: {
      type: String
    },
    buttonColor: {
      type: String,
      default: 'primary'
    },
    height: {
      type: String
    },
    width: {
      type: String
    }
  },
  data() {
    return {
      visibleLoad: false,
      takePhoto: false,
      camera: false,
      picture: false,
      inputFile: false,
      groupFacingMode: false,
      flipped: false,
      activeFlipped: false,
      activeResultPictureTab: false,
      activePhotoTab: false,
      activeVideoTab: false,
      activeCanvasTab: false,
      activeBtnLeftTab: false,
      activeBtnCameraTab: false,
      activeBtnSaveLeft: true,
      activeBtnSnapLeft: false,
      activeBtnSnapTabLeft: false,
      activeBtnSnapTabLeftBig: false,
      inputStarted: true,
      header: true,
      facingMode: true,
      QIconTab: '24px',
      timeLoadSpinner: '500',
      rotate: '0deg',
      mode: 'environment'
    }
  },
  components: {
    QIcon,
    QBtn,
    QTransition,
    QInnerLoading,
    QSpinnerGears
  },
  methods: {
    transformWidth(width){
      if (this.$props.width){
        let declaredWidth = parseInt(width, 10);
        const declaredWidthLength = declaredWidth.toString().length;
        const unitType = width.slice(declaredWidthLength);
        const defaultWidthValue = (innerWidth / 100) * 20;

        if (unitType === 'px'){}

        else if (unitType === 'vh' || unitType === '%'){
          declaredWidth = (innerWidth / 100) * declaredWidth;
        } 
        else {
          declaredWidth = (innerWidth / 100) * defaultWidthValue;
        }

        return declaredWidth > defaultWidthValue ? declaredWidth : defaultWidthValue;
      }
    },
    transformHeight(height){
      if (this.$props.height){
        let declaredHeight = parseInt(height, 10);
        const declaredHeightLength = declaredHeight.toString().length;
        const unitType = height.slice(declaredHeightLength);
        const defaultHeightValue = (innerHeight / 100) * 50;

        if (unitType === 'px') {}

        else if (unitType === 'vh' || unitType === '%'){
          declaredHeight = (innerHeight / 100) * declaredHeight;
        } 
        else {
          declaredHeight = (innerHeight / 100) * defaultHeightValue;
        }

        return declaredHeight > defaultHeightValue ? declaredHeight : defaultHeightValue;
      }
    },
    changeCamera(){
      const video = this.$refs.video;
      if (video){
        video.srcObject && video.srcObject.getTracks().forEach(t => t.stop());
        if (this.mode === 'environment'){

          this.mode = 'user';
          this.facingMode = false;
          this.rotate = '180deg';

        } 
        else {
          this.mode = 'environment';
          this.facingMode = true;
          this.rotate = '0deg';

        }
        this.activeFlipped = !this.activeFlipped;
        return this.takePictured();
      }
    },
    gotStream(stream){
      const video = this.$refs.video;
      window.stream = stream;
      video.srcObject = stream;
    },
    handleError(error){
      this.takePhoto = false;
      this.inputStarted = false;
      this.header = true;
      this.inputFile = true;
      if (this.$q.platform.is.android && AppFullscreen.toggle()){
        AppFullscreen.toggle();
      }
      Dialog.create({
        title: 'Erro com permissão de câmera',
        message: 'Verifique se você possui permissão para utilizar a câmera, veja também se o seu navegador está atualizado e tente novamente mais tarde.'
      });
    },
    deviceTouch(){
      if (this.$q.platform.is.android && !AppFullscreen.isActive()){
        this.header = false;
        this.inputFile = false;
        this.inputStarted = false;
        this.activeBtnSnapLeft = true;
        this.groupFacingMode = true;
        AppFullscreen.toggle();

      }
      else if (this.$q.platform.is.mobile && innerWidth >= 1024){

        this.groupFacingMode = true;
        this.activeResultPictureTab = true;
        this.activePhotoTab = true;
        this.activeVideoTab = true;
        this.activeCanvasTab = true;
        this.activeBtnCameraTab = true;
        this.activeBtnSnapLeft = true;
        this.activeBtnSnapTabLeft = true;
        this.activeBtnSnapTabLeftBig = true;
        this.QIconTab = 48 + 'px';

      }
      else if (this.$q.platform.is.mobile) {
        this.groupFacingMode = true;
        this.activeBtnSnapLeft = true;
        this.timeLoadSpinner = '1250';
      }
      else {
        this.groupFacingMode = false;
      }
    },
    show(){
      this.visibleLoad = true;
      this.camera = false;
      setTimeout(() => {
        this.visibleLoad = false;
        this.camera = true;
      }, this.timeLoadSpinner)
    },
    takePictured() {
      this.show();

      this.picture = false;
      this.inputFile = false;
      this.inputStarted = false;
      this.groupFacingMode = false;
      this.header = false;
      this.camera = true;
      this.takePhoto = true;

      const video = this.$refs.video;
      const canvas = this.$refs.canvas;

      let getUserMedia = navigator.webkitGetUserMedia || navigator.mozGetUserMedia;
      let constraints;

      if (video) {
        video.srcObject && video.srcObject.getTracks().forEach(t => t.stop());
        constraints = {
          audio: false,
          video:{
            width: { ideal: 1280 },
            height: { ideal: 720 },
            facingMode: this.mode
          }
        }
      }

      if (navigator.mediaDevices.getUserMedia === undefined){
        navigator.mediaDevices.getUserMedia = function(constraints){

          if (!getUserMedia){
            this.takePhoto = false;
            this.header = true;
            this.inputStarted = true;
            Promise.reject(new Error('getUserMedia não é suportado'));
            Dialog.create({
              title: 'Erro ao ativar a câmera',
              message: 'O seu navegador não está atualizado ou não possui suporte devido para ativar a câmera, tente novamente mais tarde.'
            });
          }

          return new Promise(function(resolve, reject){
            getUserMedia.call(navigator, constraints, resolve, reject);
          });
        }
      }

      if (!navigator.mediaDevices || !navigator.mediaDevices.enumerateDevices){
        Dialog.create({
          title: 'Erro ao trocar de câmera',
          message: 'O seu navegador não está atualizado ou não possui suporte devido para trocar de câmera, tente novamente mais tarde.'
        });
      }

      navigator.mediaDevices.getUserMedia(constraints).
        then(this.gotStream).catch(this.handleError);

      this.deviceTouch();

    },
    snapshot(){
      this.camera = false;
      this.picture = true;

      const video = this.$refs.video;
      const canvas = this.$refs.canvas;
      const ctx = canvas.getContext('2d');

      canvas.width = innerWidth;
      canvas.height = innerHeight;

      this.deviceTouch();

      ctx.drawImage(video, 0, 0, canvas.width, canvas.height);



    },
    saved(){
      const self = this;
      Dialog.create({
        title: 'Salvar imagem',
        message: 'Você tem certeza que deseja salvar essa imagem?',
        buttons: [
          {
            label: 'Não',
            color: 'negative',
            outline: true,
            handler(){
              Toast.create('Cancelando...');
            }
          },
          {
            label: 'Sim',
            raised: true,
            color: 'positive',
            handler(){
              const video = self.$refs.video;
              const canvas = self.$refs.canvas;

              canvas.toBlob(function(blob){
                self.$emit('pictured', blob);
              }, "image/jpeg");

              video.srcObject && video.srcObject.getTracks().forEach(t => t.stop());

              if (AppFullscreen.toggle()){
                AppFullscreen.exit();
              }

              self.takePhoto = false;
              self.inputStarted = true;

              self.header = true;
              self.camera = false;
              self.picture = false;

              Toast.create('Salvando...');
            }
          }
        ]
      });
    }
  }

}

</script>

<style lang="stylus">

:-webkit-full-screen video {
  width: 100%;
  height: 100vh
}

:-moz-full-screen video {
  width: 100%;
  height: 100vh
}
*{margin: 0; padding: 0; }


  @media only screen and (max-width: 768px){
    .video {
      height: 100vh !important;
    }
    #resultPicture {
      width: 100% !important;
      height: 100vh !important;
    }
    canvas {
      height: 100% !important;
    }
    .camera {
      width: 100% !important
    }
    .btn-camera{
      top: 86% !important;
    }
    .btn-left{
      left: 32.5% !important;
    }
  }

  @media only screen and (min-device-width: 768px){
    .btn-left{
      left: 40% !important;
    }
  }

  @media only screen and (width: 768px) and (orientation: portrait){
    .btn-camera-tab{
      top: 85% !important;
      border: 8px solid !important;

    }
    .q-btn-round.q-btn-standard{
      width: 72px !important;
      height: 72px !important;
    }
    .q-btn-round.q-btn-standard .q-icon{
      font-size: 36px !important
    }
  }

  @media only screen and (min-device-width: 768px) and (max-device-width: 1024px) and (orientation: portrait){
    .photo{
      max-width: 100% !important
    }
  }

  @media only screen and (min-device-width: 768px) and (max-device-width: 1024px) and (orientation: portrait){
    .photo{
      max-width: 100% !important
    }
  }


  @media only screen and (min-device-width: 768px) and (max-device-width: 1024px) and (orientation: landscape){
    .photo{
      max-width: 100% !important;
    }
    #resultPicture{
      width: 100% !important;
    }
    canvas{
      height: 100vh !important
    }
    .camera{
      width: 100% !important;
    }
    .video{
      height: 100vh !important;
    }
  }

  @media only screen and (min-device-width: 300px) and (max-device-width: 786px) and (orientation: landscape){
    .btn-left{
      left: 40.5% !important;
    }
    .btn-camera{
      top: 81.5% !important;
    }
  }

  .q-inner-loading{
    background-color: rgba(0, 0, 0, 0.5);
  }

  .btn-camera{
    top: 80%;
    border: 4px solid;
  }

  .btn-center{
    left: 45%;
  }

  .btn-left{
    left: 40%;
  }

  .photo-tab{
    max-width: 100% !important;
  }

  .resultPicture-tab{
    width: 100% !important
  }

  .video-tab{
    height: 100vh !important;
  }

  .canvas-tab{
    height: 100vh !important
  }

  .btn-camera-tab{
    top: 85% !important;
    border: 8px solid !important;
    width: 96px !important;
    height: 96px !important;
  }

  .btn-left-tab{
    left: 36.5% !important;
  }

  .btn-left-tab-big{
    left: 40.5% !important;
  }

  .photo {
    max-width: 700px;
    overflow-y: hidden;
    margin: 0 auto;
  }

  #resultPicture{
    margin: 0 auto;
    max-height: 100vh;
    max-width: 100%
  }

  .flipped{
    overflow: hidden;
    transform: rotateY(180deg) !important;
  }

  .btnsFacingMode{
    display: initial;

  }
  .layout-padding{
    padding: 0px;
  }

    .layout, .layout-page{
      min-height: initial !important;
      max-height: 100vh !important;
    }
    .camera {
      max-width: 100%;
      max-height: 100vh;
      text-align: center;
      overflow-y: hidden;
      overflow-x: hidden;
      margin: 0 auto;
    }
    canvas {
      width: 100%;
      height: 50vh;
    }
    .video {
      background: #222;
      width: 100%;
      height: 50vh;
      overflow-y: hidden;
      overflow-x: hidden;
      object-fit: initial;
      transform: rotateY(0deg);
      transition: 0.6s;
      object-fit: initial;
      transform-style: preserve-3d;
    }

</style>
