<script>
  import { Progress } from "sveltestrap";
  import { onMount } from "svelte";

  let loadingPercentage = 0;
  let canvas;
  let percentage = 0;
  let images = [];
  let variables = null;
  let skipSteps = 0;

  // 计算 slider 的填充百分比（供 CSS 渐变用）
  $: skipPercent = Math.round((skipSteps / 20) * 100) + "%";

  // 更易读的模式说明
  $: modeLabel =
    skipSteps === 0
      ? "标准采样 (DDPM)"
      : `加速采样 (DDIM，跳过 ${skipSteps} 步)`;

  // 使用原来的 Worker 路径
  const worker = new Worker(
    "./64x64_cosin_300/worker.js?_" + new Date().getTime()
  );

  // 每次 skipSteps 变动就通知 worker
  $: worker.postMessage({ skipSteps });

  onMount(() => {
    // 把 canvas 交给 worker 绘制
    const offscreen = canvas.transferControlToOffscreen();
    worker.postMessage({ offscreen }, [offscreen]);

    worker.onmessage = (evt) => {
      const data = evt.data;
      switch (data.type) {
        case "image":
          percentage = data.percent;
          if (data.imageBlob) {
            const reader = new FileReader();
            reader.onloadend = () => {
              images = [...images, reader.result];
            };
            reader.readAsDataURL(data.imageBlob);
          }
          break;
        case "ready":
          loadingPercentage = 100;
          variables = data.parameters;
          break;
        case "progress":
          loadingPercentage = Math.floor(data.progress * 100);
          break;
        case "error":
          alert(data.message);
          location.reload();
          break;
      }
    };
  });
</script>

<div
  class="border-top-0 p-5 rounded shadow-sm"
  style="background-color: rgba(255,255,255,0.8);"
>
  {#if loadingPercentage < 100}
    <!-- 加载中 -->
    <div class="d-flex align-items-center mb-3">
      <div class="flex-grow-1 me-3">
        <Progress animated value={loadingPercentage} color="pink">
          {loadingPercentage}%
        </Progress>
      </div>
      <i
        class="bi bi-info-circle"
        title="步数越大速度越快，但可能会牺牲一点图像质量"
      ></i>
    </div>
  {:else}
    <!-- 加速滑条 -->
    <div class="mb-3 row align-items-center">
      <label for="acc" class="col-sm-2 text-end">加速等级：</label>
      <div class="col-sm-8">
        <input
          id="acc"
          type="range"
          class="form-range"
          min="0"
          max="20"
          bind:value={skipSteps}
          style="--value-percent: {skipPercent}"
        />
      </div>
      <div class="col-sm-2 text-start">
        <small><strong>{modeLabel}</strong></small>
      </div>
    </div>
  {/if}

  <hr />

  <!-- 结果输出区 -->
  <div class="container mt-2">
    <div class="row">
      {#each images as img}
        <div class="col">
          <img src={img} class="generated mb-2" alt="avatar" />
        </div>
      {/each}
      <div class="col">
        <div class="d-flex justify-content-center">
          <div>
            <canvas
              bind:this={canvas}
              class="avatar mb-2"
              width="128"
              height="128"
            />
            <div
              class="progress_bar"
              style="width: {percentage * 100}%"
            ></div>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>

<style>
    .progress_bar {
        height: 13px;
        background-color: pink !important;
    }

    .generated {
        width: 128px;
        height: 128px;
        border: solid 1px #666;
    }

    .avatar {
        width: 128px;
        height: 128px;
        border: solid 1px #666;
    }

    .progress_bar {
        height: 13px;
        background-color: pink !important;
    }

    /* ****************************************** */
    /* —— 全局覆盖弹性滑条 —— */
    /* 1. 透明化原生 track，只留下我们自己的 */
    :global(input[type="range"]) {
        -webkit-appearance: none;
        width: 100%;
        background: transparent;
        height: 8px;
        margin: 0;
    }

    /* 2. 滑块本体（Thumb） */
    :global(input[type="range"]::-webkit-slider-thumb) {
        -webkit-appearance: none;
        width: 20px; height: 20px;
        border-radius: 50%;
        background: pink !important;
        margin-top: -6px;
        cursor: pointer;
    }
    :global(input[type="range"]::-moz-range-thumb) {
        width: 20px; height: 20px;
        border-radius: 50%;
        background: pink !important;
        cursor: pointer;
    }

    /* 3. 整条轨道的灰色底色 */
    :global(input[type="range"]::-webkit-slider-runnable-track),
    :global(input[type="range"]::-moz-range-track) {
        width: 100%;
        height: 8px;
        background: #e0e0e0 !important;
        border-radius: 4px;
    }

    /* 4. Firefox 原生已填充部分 */
    :global(input[type="range"]::-moz-range-progress) {
        background: pink !important;
        height: 8px;
        border-radius: 4px 0 0 4px;
    }

    /* 5. WebKit 用渐变 hack 出已填充部分 */
    :global(input[type="range"]::-webkit-slider-runnable-track) {
    background: linear-gradient(
        to right,
        pink var(--value-percent, 0%),
        #e0e0e0 var(--value-percent, 0%)
    ) !important;
    }
</style>
