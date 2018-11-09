# Analyzing Runtime Performance

## Get started

1. Open Google Chrome in Incognito Mode.
2. Load the following page in your Incognito window.
```code
https://googlechrome.github.io/devtools-samples/jank/
```
3. Press Command+Option+I (Mac) or Control+Shift+I (Windows, Linux) to open DevTools.

### Simulate a mobile CPU
1. In DevTools, click the Performance tab.
2. For CPU, select 2x slowdown. DevTools throttles your CPU so that it's 2 times slower than usual.
![CPU throttling](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/imgs/throttling.svg?hl=zh-cn)

### Record runtime performance
1. In DevTools, click Record Record. DevTools captures performance metrics as the page runs.
2. Click Stop. DevTools stops recording, processes the data, then displays the results on the Performance panel.
![](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/imgs/results.png?hl=zh-cn)

### Analyze the results

#### Analyze frames per second
1. Look at the FPS chart. Whenever you see a red bar above FPS, it means that the framerate dropped so low that it's probably harming the user experience. In general, the higher the green bar, the higher the FPS.
![](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/imgs/fps-chart.svg?hl=zh-cn)

2. Below the FPS chart you see the CPU chart. The colors in the CPU chart correspond to the colors in the Summary tab, at the bottom of the Performance panel. The fact that the CPU chart is full of color means that the CPU was maxed out during the recording. Whenever you see the CPU maxed out for long periods, it's a cue to find ways to do less work.
![](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/imgs/cpu-summary.svg?hl=zh-cn)

#### Find the bottleneck
1. Note the summary tab. When no events are selected, this tab shows you a breakdown of activity. The page spent most of its time rendering. Since performance is the art of doing less work, your goal is to reduce the amount of time spent doing rendering work.
![](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/imgs/summary.svg?hl=zh-cn)

2. Expand the Main section. DevTools shows you a flame chart of activity on the main thread, over time. The x-axis represents the recording, over time. Each bar represents an event. A wider bar means that event took longer. The y-axis represents the call stack. When you see events stacked on top of each other, it means the upper events caused the lower events.
![](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/imgs/main.svg?hl=zh-cn)

3. There's a lot of data in the recording. Zoom in on a single Animation Frame Fired event by clicking, holding, and dragging your mouse over the Overview, which is the section that includes the FPS, CPU, and NET charts. The Main section and Summary tab only display information for the selected portion of the recording.
![](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/imgs/zoomed.png?hl=zh-cn)

4. Note the red triangle in the top-right of the Animation Frame Fired event. Whenever you see a red triangle, it's a warning that there may be an issue related to this event.

5. Click the Animation Frame Fired event. The Summary tab now shows you information about that event. Note the reveal link. Clicking that causes DevTools to highlight the event that initiated the Animation Frame Fired event. Also note the app.js:94 link. Clicking that jumps you to the relevant line in the source code.
![](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/imgs/animation-frame-fired.png?hl=zh-cn)

6. Under the app.update event, there's a bunch of purple events. If they were wider, it looks as though each one might have a red triangle on it. Click one of the purple Layout events now. DevTools provides more information about the event in the Summary tab. Indeed, there's a warning about forced reflows (another word for layout).

7. In the Summary tab, click the app.js:70 link under Layout Forced. DevTools takes you to the line of code that forced the layout.
![](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/imgs/forced-layout-src.png?hl=zh-cn)

> Note: The problem with this code is that, in each animation frame, it changes the style for each square, and then queries the position of each square on the page. Because the styles changed, the browser doesn't know if each square's position changed, so it has to re-layout the square in order to compute its position. See Avoid forced synchronous layouts to learn more.
