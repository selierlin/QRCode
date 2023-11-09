<script>
import { defineComponent, reactive, toRefs, nextTick } from "vue";
import QRCodeVue3 from "qrcode-vue3";

export default defineComponent({
  name: "viewQrcode",
  components: {
    QRCodeVue3,
  },
  setup() {
    const data = reactive({
      /**
       * 基础配置
       * https://qr-code-styling.com
       */
      width: 360, // 二维码宽度
      height: 360, // 二维码高度
      value: "123", // 二维码内容

      margin: 6, // 二维码图像的外边距

      /**
       * 背景配置
       */
      backgroundOptions: {
        //二维码背景色
        color: "white",
      },

      /**
       * 二维码配置
       */
      qrOptions: {
        typeNumber: "0", // 类型编号 0 - 40
        mode: "Byte", // 模式 Numeric | Alphanumeric | Byte | Kanji
        errorCorrectionLevel: "Q", // 错误级别 L | M | Q | H
      },

      /**
       * 图像配置（中心图片）
       */
      imageOptions: {
        hideBackgroundDots: true, // 隐藏图片背后有点
        imageSize: 0.5,
        margin: 5,
        crossOrigin: "anonymous", // anonymous | use-credentials
      },
      /**
       * 二维码点配置
       */
      dotsOptions: {
        type: "square", // 二维码样式 square | dots | rounded | extra-rounded | classy | classy-rounded
        color: "#000000"
      },
      /**
       * 角落广场配置
       */
      cornersSquareOptions: {
        type: "square", // none | square | dot | extra-rounded
        color: "#000000"
      },
      /**
       * 角落点配置
       */
      cornersDotOptions: {
        type: "square", // none | square | dot
        color: "#000000"
      },
    });

    const upFile = (event) => {
      const value = data.value,
        [file] = event.target.files || event.dataTransfer.files;
      try {
        data.image = URL.createObjectURL(file);
      } catch (err) {
        const fr = new FileReader(err);
        fr.readAsDataURL(file);
        fr.onload = ({ target }) => {
          data.image = target.result;
        };
      } finally {
        data.value = "";
        nextTick(() => {
          data.value = value;
        });
      }
    };

    return {
      upFile,
      ...toRefs(data),
    };
  },
});
</script>

<template>
  <main class="qrcode">
    <div class="form">
      <input
        type="text"
        v-model="value"
        placeholder="请在这里输入要生成的内容！"
      />
      <button>
        添加图标
        <input type="file" accept="image/*" @change="upFile" />
      </button>
    </div>

    <div class="code" ref="code">
      <QRCodeVue3
        :margin="margin"
        :width="width"
        :height="height"
        :value="value"
        :qrOptions="qrOptions"
        :image="image"
        :imageOptions="imageOptions"
        :dotsOptions="dotsOptions"
        :backgroundOptions="backgroundOptions"
        :cornersSquareOptions="cornersSquareOptions"
        :cornersDotOptions="cornersDotOptions"
      />
    </div>
  </main>
</template>

<style lang="less">
.qrcode {
  .form {
    margin: 30px auto;
    width: 86%;

    input {
      box-sizing: border-box;
      padding: 0 10px;
      width: 80%;
      height: 50px;
      font-size: 18px;
      border: 1px solid #767676;
      border-radius: 6px 0 0 6px;
    }

    button {
      box-sizing: border-box;
      position: relative;
      width: 20%;
      height: 50px;
      font-size: 18px;
      vertical-align: top;
      color: white;
      background: #42b983;
      border: 1px solid #767676;
      border-radius: 0 6px 6px 0;
      border-left: none;
      overflow: hidden;
      cursor: pointer;

      input {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        cursor: pointer;
        opacity: 0;
      }
    }
  }

  .code {
    margin: auto;
    padding: 8px;
    width: 360px;
    height: 360px;
    border-radius: 6px;
    border: 1px solid gray;
  }
}
</style>
