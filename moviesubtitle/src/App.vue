<template>
  <Container>
    <canvas
        id="canvas"
        ref="canvas"
        :width="1000"
        :height="427"
    >
    </canvas>

    <blockquote v-html="images[imageIndex].original"></blockquote>

    <select @change="onImageChanged($event.target.value)">
      <option v-for="img in images" :key="img.id" :value="img.id">
        {{img.name}}
      </option>
    </select>

    <textarea
        :value="option.text"
        @input="onValueChanged('text', $event.target.value)"
    />

    <select
        :value="option.fontFamily"
        @input="onValueChanged('fontFamily', $event.target.value)"
    >
      <option value="Nanum Gothic">Nanum Gothic</option>
      <option value="Gulim">Gulim</option>
      <option value="Arial">Arial</option>
      <option value="Helvetica">Helvetica</option>
    </select>

    <select
        :value="option.fontSize"
        @input="onValueChanged('fontSize', $event.target.value)"
    >
      <option value="15">15</option>
      <option value="20">20</option>
      <option value="25">25</option>
      <option value="30">30</option>
      <option value="35">35</option>
      <option value="40">40</option>
      <option value="45">45</option>
    </select>

    <button @click="downloadCanvas">다운로드</button>
  </Container>
</template>

<script>
import styled from 'vue-styled-components';
import images from './images'

const Container = styled.div`;
  display: flex;
  justify-content: center;
  align-items: center;
`;

export default {
  name: 'App',
  data() {
    return {
      imageIndex: 0,
      option : {
        fontFamily: 'Gulim',
        fontSize: 30,
        fontColor: '#FFFFFF',
        fontWeight: 'normal',
        text: '',
      }
    }
  },
  components: {
    Container,
  },
  computed: {
    images() {
      return images;
    }
  },
  mounted() {
      this.updateCanvas();
  },

  methods: {
    updateCanvas() {
      if(!this.$refs.canvas) return;
      this.updateCanvasImage()
    },

    updateCanvasImage() {
      const { canvas }  = this.$refs;
      const ctx = canvas.getContext("2d");
      const img = new Image();
      img.src = this.images[this.imageIndex].src;
      img.onload = () => {
        ctx.drawImage(img, 0, 0);
        this.updateCanvasText();
      }
    },

    onImageChanged(value) {
      this.imageIndex = value;
      this.updateCanvas();
    },

    onValueChanged(key ,value) {
      this.option[key] = value;
      this.updateCanvas();
    },

    updateCanvasText() {
      const { canvas } = this.$refs;

      const { text, fontFamily, fontSize, fontColor, fontWeight, textBorder } = this.option;

      const ctx = canvas.getContext('2d');

      ctx.textAlign = 'center'; // 가로 가운데 정렬
      ctx.textBaseline = 'middle'; // 세로 가운데 정렬
      ctx.font = `${fontWeight} ${fontSize}px ${fontFamily}`;

      const lines = text.split('\n');
      console.log(lines);

      lines.forEach((line, index) => {
        ctx.shadowColor = 'black';
        ctx.shadowBlur = 10;
        ctx.lineWidth = 5;
        ctx.strokeStyle = `${textBorder}`;
        ctx.strokeText(
            line,
            canvas.width / 2,
            canvas.height - fontSize * (lines.length - index) * 1.5,
        );

        ctx.fillStyle = fontColor;
        ctx.fillText(
            line,
            canvas.width / 2,
            canvas.height - fontSize * (lines.length - index) * 1.5,
        );
      });
    },
    downloadCanvas() {
      const url = this.$refs.canvas.toDataURL('image/png');
      const link = document.createElement('a');
      link.href = url;
      link.setAttribute('download', `${this.images[this.imageIndex].name}.png`);
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
      // https://velog.io/@kkaemi/javascript-a-tag%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC-javascript%EC%97%90%EC%84%9C-%ED%8C%8C%EC%9D%BC-%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%ED%95%98%EA%B8%B0
      // https://www.delftstack.com/ko/howto/javascript/javascript-download/
    },
  },
}
</script>

<style>
</style>
