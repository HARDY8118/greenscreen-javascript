<script lang="ts">
  import { onMount } from "svelte";

  import fg from "./assets/fg.mp4";
  import bg from "./assets/bg.mp4";

  let video: HTMLVideoElement;
  let transtitionCanvas: HTMLCanvasElement;
  let outputCanvas: HTMLCanvasElement;
  let color: string = "#000000";
  let height: number = 640;
  let width: number = 360;

  let transitionCtx: CanvasRenderingContext2D;
  let canvasCtx: CanvasRenderingContext2D;

  let tmpCanvas: HTMLCanvasElement = document.createElement("canvas");
  tmpCanvas.setAttribute("height", height + "");
  tmpCanvas.setAttribute("width", width + "");
  let tmpCtx: CanvasRenderingContext2D = tmpCanvas.getContext("2d");

  let tmpVideo: HTMLVideoElement = document.createElement("video");
  tmpVideo.src = bg;
  tmpVideo.autoplay = true;
  tmpVideo.loop = true;
  tmpVideo.muted = true;

  const render = () => {
    let colorRgb: number[] = [
      color.slice(1, 3),
      color.slice(3, 5),
      color.slice(5, 7),
    ].map((_) => parseInt(_, 16));

    tmpCtx.drawImage(video, 0, 0, width, height);
    let frame: ImageData = tmpCtx.getImageData(0, 0, width, height);

    tmpCtx.drawImage(tmpVideo, 0, 0, width, height);
    let tmpFrame: ImageData = tmpCtx.getImageData(0, 0, width, height);

    for (let i = 0; i < frame.data.length; i += 4) {
      let r: number = frame.data[i + 0];
      let g: number = frame.data[i + 1];
      let b: number = frame.data[i + 2];
      // let a = data[i + 3];

      if (r === 81 && g === 189 && b === 8) {
        frame.data[i] = colorRgb[0];
        frame.data[i + 1] = colorRgb[1];
        frame.data[i + 2] = colorRgb[2];
      } else {
        tmpFrame.data[i] = frame.data[i];
        tmpFrame.data[i + 1] = frame.data[i + 1];
        tmpFrame.data[i + 2] = frame.data[i + 2];
      }
    }

    transitionCtx.putImageData(frame, 0, 0);
    canvasCtx.putImageData(tmpFrame, 0, 0);

    requestAnimationFrame(render);
  };

  onMount(() => {
    transitionCtx = transtitionCanvas.getContext("2d");
    canvasCtx = outputCanvas.getContext("2d");

    video.addEventListener("play", render);
  });
</script>

<main>
  <div class="container">
    <video bind:this={video} src={fg} {height} {width} autoplay muted loop />
    <section>
      <canvas bind:this={transtitionCanvas} {height} {width} />
      <input type="color" name="color" id="color" bind:value={color} />
      {color}
    </section>
    <section>
      <canvas bind:this={outputCanvas} {height} {width} />
    </section>
  </div>
</main>

<style>
  *,
  *::before,
  *::after {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  main {
    height: 100vh;
    display: flex;
    flex-direction: column;
  }
  .container {
    height: 100vh;
    display: flex;
    flex-direction: row;
    justify-content: space-between;
  }
  section {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
</style>
