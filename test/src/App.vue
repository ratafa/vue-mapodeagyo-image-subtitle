<template>
  <Container>
    <canvas
        ref="canvas"
        :width="1000"
        :height="800">
    </canvas>

    <Select @change="changeCanvas($event.target.value)">
      <Option v-for="img in images" :key="img.id" :value="img.id">
        {{img.name}}
      </Option>
    </Select>

    <textarea
        @input="changeText('text', $event.target.value)"
        :value="this.option.text"
    />

    <select
      :value="this.option.fontFamily"
      @input="changeText('fontFamily', $event.target.value)"
    >
      <option value="Nanum Gothic">Nanum Gothic</option>
      <option value="Gulim">Gulim</option>
      <option value="Arial">Arial</option>
      <option value="Helvetica">Helvetica</option>
    </select>

    <select
        :value="this.option.fontSize"
        @input="changeText('fontFamily', $event.target.value)"
    >
      <option value="20">20</option>
      <option value="15">15</option>
      <option value="25">25</option>
      <option value="30">30</option>
      <option value="35">35</option>
      <option value="40">40</option>
      <option value="45">45</option>
    </select>

  </Container>
</template>

<script>
import styled from 'vue-styled-components';
import images from "@/images";

const Container = styled.div`
  height: 100%;
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
`;

const Select = styled.select`

`;
const Option = styled.option`
`;


export default {
  name: 'App',
  components: {
    Container,
    Select,
    Option,
  },
  data() {
    return{
      indexNum : 0,
      option : {
        fontFamily: 'Gulim',
        fontSize: 30,
        fontColor: '#FFFFFF',
        fontWeight: 'normal',
        text: '',
      }
    }
  },
  computed: {
    images() {
      return images;
    }
  },
  mounted() {
    this.makeCanvas();
  },
  methods:{
    makeCanvas() {
      if (!this.$refs.canvas) return;
      this.updateCanvasImg()
    },
    updateCanvasImg() {
      const {canvas} = this.$refs;
      const ctx = canvas.getContext('2d');
      const img = new Image();
      img.src = this.images[this.indexNum].src;
      img.onload = () => {
        ctx.drawImage(img, 0, 0);
        this.updateLetter();

      }
    },
    changeCanvas(value) {
      this.indexNum = value;
      this.makeCanvas()
    },
    changeText(key, value) {
      this.option[key] = value;
      this.makeCanvas();
    },
    updateLetter() {
      const {canvas} = this.$refs;
      const { text, fontFamily, fontSize, fontColor, fontWeight, textBorder } = this.option;
      const ctx = canvas.getContext('2d');
      ctx.textAlign = 'center'; // 가로 가운데 정렬
      ctx.textBaseline = 'middle'; // 세로 가운데 정렬
      ctx.font = `${fontWeight} ${fontSize}px ${fontFamily}`;

      const lines = text.split('\n');

      lines.forEach((line, idx) => {
        ctx.shadowColor = 'black';
        ctx.shadowBlur = 10;
        ctx.lineWidth = 5;
        ctx.strokeStyle = `${textBorder}`;
        ctx.strokeText(
            line,
            canvas.width / 2,
            canvas.height - fontSize * (lines.length - idx) * 1.5,
        );

        ctx.fillStyle = fontColor;
        ctx.fillText(
            line,
            canvas.width / 2,
            canvas.height - fontSize * (lines.length - idx) * 1.5,
        );
      })
    },
  }
}
</script>
