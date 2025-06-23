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

  // 三个 step：0–显示第一个确认框，1–功能介绍，2–其他说明，3–进入主界面
  let step = 0;

  // 绑定 <audio> 用于点击后播放
  let audioEl;

  function handleConfirmEnter() {
    // 第一个模态框→第二个
    step = 1;
    audioEl.play().catch(() => {});
  }

  function handleIntroduce() {
    // 第二个模态框→第三个
    step = 2;
  }

  function handleEnterMain() {
    // 第三个模态框→正式界面
    step = 3;
  }
</script>

<svelte:head>
  <link rel="stylesheet" href="./bootstrap/dist/css/bootstrap.min.css" />
</svelte:head>

<style>
  /* 粉色按钮 */
  :global(.btn-pink) {
    background-color: pink;
    border-color: pink;
    color: white;
  }
  /* 隐藏原生 audio 控制条 */
  audio {
    display: none;
  }
  /* 主界面的半透明背景 */
  :global(.tab-pane) {
    background-color: rgba(255, 255, 255, 0.8);
  }
</style>

<!-- 自动循环播放的 BGM，点击后才会触发 -->
<audio bind:this={audioEl} src="bgm.mp3" loop></audio>

<!-- 第一个弹窗：确认进入？ -->
<Modal isOpen={step === 0} backdrop="static" keyboard={false}>
  <ModalHeader>Welcome!</ModalHeader>
  <ModalBody class="text-center">
    <p>Do you confirm to enter「The Magical Girls Generator」?</p>
  </ModalBody>
  <ModalFooter>
    <Button class="btn-pink" on:click={handleConfirmEnter}>
      Yes!
    </Button>
  </ModalFooter>
</Modal>

<!-- 第二个弹窗：功能介绍 + 图片 -->
<Modal isOpen={step === 1} size="lg" backdrop="static" keyboard={false}>
  <ModalHeader>Hello, New Incubator!</ModalHeader>
  <ModalBody>
    <p>
      As you know, with the development of cosmic civilization, we no longer need
      to sign contracts verbally. We simply select humans at random and turn
      them into magical girls. It's that simple!
    </p>
    <p>
      The moment you enter the website, our selection process begins. You will
      see avatars appearing in the window, which signifies that the magical
      girls have successfully registered.
    </p>
    <img
      src="welcome.png"
      alt="欢迎图"
      style="
        display: block;
        width: 100%;
        max-width: 400px;
        margin: 1rem auto 0;
        border-radius: 8px;
      "
    />
  </ModalBody>
  <ModalFooter>
    <Button class="btn-pink" on:click={handleIntroduce}>
      Okay…? (So, all genders → girls…?)
    </Button>
  </ModalFooter>
</Modal>

<!-- 第三个弹窗：其他注意事项 -->
<Modal isOpen={step === 2} size="lg" backdrop="static" keyboard={false}>
  <ModalHeader>Other Things You Need to Know</ModalHeader>
  <ModalBody>
    <p>
      You can drag the progress bar to control the speed at which magical girls are generated.
      However, if you see an unusual face, please don’t mind it. That’s just how magical girls are.
    </p>
    <p>Let’s work together to prevent the heat death of the universe!</p>
    <img
      src="welcome.png"
      alt="欢迎图"
      style="
        display: block;
        width: 100%;
        max-width: 400px;
        margin: 1rem auto 0;
        border-radius: 8px;
      "
    />
  </ModalBody>
  <ModalFooter>
    <Button class="btn-pink" on:click={handleEnterMain}>
      Glad to help!
    </Button>
  </ModalFooter>
</Modal>

<!-- step === 3 时渲染真正的主界面 -->
{#if step === 3}
  <TabContent class="w-100 mt-3">
    <TabPane tabId="backward" active>
      <span slot="tab">🪄 Magical Girls Generator ✨</span>
      <ReverseProcess />
    </TabPane>
  </TabContent>
{/if}
