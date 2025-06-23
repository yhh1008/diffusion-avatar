<script>
  import {
    Progress,
    Button,
    TabContent,
    TabPane,
    Icon,
    Table,
  } from "sveltestrap";
  import { onMount } from "svelte";
  import ReverseProcess from "./ReverseProcess.svelte";

  let loadingPercentage = 0;
  let canvas;
  let percentage = 0;
  let images = [];
  let variables = null;

  // audio 元素的引用
  let audioEl;

  onMount(() => {
    if (audioEl) {
      audioEl
        .play()
        .catch(() => {
          // 如果被浏览器拦截，就等用户下一次点击页面时再播放
          const tryPlay = () => {
            audioEl.play();
            window.removeEventListener("click", tryPlay);
          };
          window.addEventListener("click", tryPlay);
        });
    }
  });
</script>

<svelte:head>
  <link
    rel="stylesheet"
    href="./bootstrap/dist/css/bootstrap.min.css"
  />
</svelte:head>

<!-- 直接从 public/bgm.mp3 引用，无需 import -->
<audio
  bind:this={audioEl}
  src="bgm.mp3"
  autoplay
  loop
  style="display:none;"
></audio>

<style>
  :global(.tab-pane) {
    background-color: rgba(255, 255, 255, 0.8);
  }
</style>

<TabContent class="w-100 mt-3">
  <TabPane tabId="backward" active>
    <span slot="tab">
      🎨 Kawaii Gal Avatar Generator！
    </span>
    <ReverseProcess />
  </TabPane>
</TabContent>
