<script>
    import { Table, Progress, Icon } from "sveltestrap";
    import { onMount } from "svelte";
    import reverse_jpg from "./assets/reverse.jpg";
    import ddim_png from "./assets/ddim.png";

    let loadingPercentage = 0;
    let canvas;
    let percentage = 0;
    let images = [];
    let variables = null;
    let skipSteps = 0;

    const worker = new Worker(
        "./64x64_cosin_300/worker.js?_" + new Date().getTime(),
    );

    $: skipSteps,
        (() => {
            worker.postMessage({ skipSteps: skipSteps });
        })();

    onMount(() => {
        const offscreen = canvas.transferControlToOffscreen();
        worker.postMessage({ offscreen: offscreen }, [offscreen]);
        worker.onmessage = async (evt) => {
            const data = evt.data;
            switch (data.type) {
                case "image": {
                    percentage = data.percent;
                    if (data.imageBlob) {
                        const reader = new FileReader();
                        reader.onloadend = () => {
                            images.push(reader.result);
                            images = images;
                        };
                        reader.readAsDataURL(data.imageBlob);
                    }
                    break;
                }
                case "ready": {
                    loadingPercentage = 100;
                    variables = data.parameters;
                    break;
                }
                case "progress": {
                    loadingPercentage = Math.floor(data.progress * 100);
                    break;
                }
                case "error": {
                    alert(data.message);
                    self.location = self.location;
                    break;
                }
                default: {
                    console.log(data);
                    break;
                }
            }
        };
    });
</script>

<div class="border border-top-0 p-5">
    {#if loadingPercentage < 100}
        <div>
            <div class="text-center">Loading ...</div>
            <Progress animated color="success" value={loadingPercentage}
                >{loadingPercentage}%</Progress
            >
        </div>
    {:else}
        <div class="mb-3 row">
            <label for="acc" class="col-sm-2 text-end">Acceleration :</label>
            <div class="col-sm-8">
                <input
                    type="range"
                    class="form-range"
                    min="0"
                    max="20"
                    step="1"
                    id="acc"
                    bind:value={skipSteps}
                />
            </div>
            <div class="col-sm-2 text-start">
                <small>
                    <strong
                        >{skipSteps <= 0
                            ? "DDPM"
                            : "DDIM; m=n-" + skipSteps.toString()}</strong
                    >
                </small>
            </div>
        </div>
    {/if}
    <hr />
    <div class="container mt-2">
        <div class="row">
            {#each images as img}
                <div class="col">
                    <img src={img} class="generated mb-2" alt="" />
                </div>
            {/each}
            <div class="col">
                <div class="d-flex justify-content-center">
                    <div>
                        <canvas
                            class="d-flex avatar"
                            bind:this={canvas}
                            width="128"
                            height="128"
                        />
                        <div
                            class="d-flex progress_bar"
                            style="width:{percentage * 100}%"
                        />
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

<style>
    .progress_bar {
        height: 13px;
        background-color: pink;
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
        
    input[type="range"] {
        accent-color:pink;
    }

</style>
