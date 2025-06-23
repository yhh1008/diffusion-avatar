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
  import wallpaper from "./assets/wallpaper.jpg";
  import ReverseProcess from "./ReverseProcess.svelte";

  let loadingPercentage = 0;
  let canvas;
  let percentage = 0;
  let images = [];
  let variables = null;
</script>

<svelte:head>
  <!-- 不再用本地 ./bootstrap 路径，改为 CDN，确保在 GitHub Pages 上总能拉到 -->
  <link
    href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css"
    rel="stylesheet"
    integrity="sha384-…"
    crossorigin="anonymous"
  />
</svelte:head>

<!-- 
  把背景交给 body 来撑满全屏
  TabContent 内部就不用再写背景图了 
-->
<style>
  :global(body) {
    margin: 0;
    padding: 0;
    /* 你也可以改为固定背景：fixed */
    background: url("{wallpaper}") no-repeat center center;
    background-size: cover;
    min-height: 100vh;
  }
  /* 让 TabPane 整体内容放到半透明白底上 */
  :global(.tab-pane) {
    background-color: rgba(255, 255, 255, 0.8);
  }
</style>

<!-- 去掉 Icon 里的 arrow-left -->
<TabContent class="w-100 mt-3">
  <TabPane tabId="backward" active>
    <span slot="tab">
      🎨 Kawaii Gal Avatar Generator！
    </span>
    <ReverseProcess />
  </TabPane>
</TabContent>