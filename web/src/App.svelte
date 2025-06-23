<script>
  import { onMount } from "svelte";
  import {
    Modal,
    ModalHeader,
    ModalBody,
    ModalFooter,
    Button,
    TabContent,
    TabPane,
  } from "sveltestrap";
  import ReverseProcess from "./ReverseProcess.svelte";

  // step 0/1/2 三个状态
  let step = 0;

  // 绑定 <audio> 元素
  let audioEl;

  function handleConfirmEnter() {
    // 用户第一次点击「确认进入」
    step = 1;
    // 试图马上播放 bgm
    audioEl.play().catch(() => {
      // 如果因为浏览器策略被禁止自动播放就忽略
    });
  }

  function handleAcknowledge() {
    // 用户点击「我已知晓」
    step = 2;
  }
</script>

<svelte:head>
  <link
    rel="stylesheet"
    href="./bootstrap/dist/css/bootstrap.min.css"
  />
</svelte:head>

<!-- 1. bgm 元素，隐藏控制条 -->
<audio
  bind:this={audioEl}
  src="bgm.mp3"
  loop
  style="display:none;"
></audio>


<!-- 第一个弹窗：确认进入？ -->
<Modal isOpen={step === 0} backdrop="static" keyboard={false}>
  <ModalHeader>欢迎</ModalHeader>
  <ModalBody class="text-center">
    <p>确认进入「Kawaii Gal Avatar Generator」？</p>
  </ModalBody>
  <ModalFooter>
    <Button color="primary" on:click={handleConfirmEnter}>
      确认进入
    </Button>
  </ModalFooter>
</Modal>

<!-- 第二个弹窗：功能介绍 + 图片 -->
<Modal isOpen={step === 1} size="lg" backdrop="static" keyboard={false}>
  <ModalHeader>使用说明</ModalHeader>
  <ModalBody>
    <p>1. 这是一个用浏览器 Diffusion 模型生成可爱头像的演示。</p>
    <p>2. 你可以拖动下方「加速等级」来控制采样步数。</p>
    <img
      src="welcome.png"
      alt="欢迎图"
      style="width: 100%; border-radius: 8px; margin-top:1rem;"
    />
  </ModalBody>
  <ModalFooter>
    <Button color="primary" on:click={handleAcknowledge}>
      我已知晓
    </Button>
  </ModalFooter>
</Modal>


{#if step === 2}
  <!-- 正式主界面 -->
  <TabContent class="w-100 mt-3">
    <TabPane tabId="backward" active>
      <span slot="tab">🎨 Kawaii Gal Avatar Generator！</span>
      <ReverseProcess />
    </TabPane>
  </TabContent>
{/if}
