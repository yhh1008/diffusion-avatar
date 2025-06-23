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
  <link rel="stylesheet" href="./bootstrap/dist/css/bootstrap.min.css" />
</svelte:head>

<TabContent
  class="w-100 mt-3"
  style="background:
    url({wallpaper}) no-repeat center center;
    background-size: cover;"
>
  <TabPane tabId="backward" active>
    <span slot="tab">
      🎨 Kawaii Gal Avatar Generator！
    </span>

    <ReverseProcess />
  </TabPane>
</TabContent>

<style>
  :global(.tab-pane) {
    background-color: rgba(255, 255, 255, 0.8);
  }
</style>