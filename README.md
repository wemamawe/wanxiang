# 万象 | Wanxiang

> 诗词 · 水墨 · 交互 · 游戏 — 以文字为砖的数字艺术展览馆
> Interactive web art: Chinese poetry, ink painting, games, and digital installations.

**[✨ Live Demo](https://wemamawe.github.io/wanxiang/)**

---

## About

Blossom Gallery is a collection of interactive visual art pieces, each a single HTML file with zero external dependencies. The scenes draw from Chinese poetry, impressionist painting, ink wash aesthetics, and modern data visualization — all rendered with Canvas 2D, Three.js, and Web Audio API.

花·树·展览馆是一组交互式网页艺术作品，每件作品是一个零依赖的单文件 HTML。灵感来源于中国诗词、印象派绘画、水墨美学和现代数据可视化，使用 Canvas 2D、Three.js 和 Web Audio API 渲染。

## Scenes

| # | Scene | Theme | Highlight |
|---|-------|-------|-----------|
| 1 | 🌸 [Sakura Story](sakura3d.html) | Nature | Three.js 3D cherry blossom |
| 2 | 🍑 [Peach Seasons](peach.html) | Nature | Five-stage life cycle |
| 3 | 🍂 [Ginkgo Autumn](ginkgo.html) | Nature | Three.js seasonal changes |
| 4 | 🌿 [Fractal Growth](fractal.html) | Nature | L-System recursive algorithm |
| 5 | ❤️ [Heart Blossom](heart.html) | Nature | Pretext museum-style layout |
| 6 | 🌸 [Upward View](upward.html) | Nature | Looking up through branches |
| 7 | 🌸 [Spring Loading](spring.html) | Nature | UI progress bar → blossom burst |
| 8 | 🪷 [Lotus Pond](lotus.html) | Nature | Monet impressionist, fish AI, ripples |
| 9 | 🕊️ [Swallow Return](swallow.html) | Poetry | Zhang Ailing farewell letter, character → swallow morph |
| 10 | 🖋️ [Ink Plum](ink-tree.html) | Poetry | Ink wash, xuan paper texture, seal |
| 11 | 🌊 [Tide](tide.html) | Poetry | Su Shi · Red Cliff, wave text, moon |
| 12 | 🕯️ [Candle](candle.html) | Poetry | Nalan Xingde · candlelight physics |
| 13 | 🦋 [Lost in Time](wangran.html) | Poetry | Li Shangyin · ink smoke, vertical poem, butterflies |
| 14 | 🌀 [Vortex](vortex.html) | Emotion | 400 particles, character scatter |
| 15 | 🌧️ [Rain Glass](wet.html) | Emotion | Raindrop physics, city lights |
| 16 | 🌙 [Old Dream](oldream.html) | Emotion | Turner-style light fog, text dissolve |
| 17 | 🕸️ [Trap](trap.html) | Emotion | Text mosaic → lace web → bloom, dove & butterfly |
| 18 | 🌓 [Wish & Sorrow](wish.html) | Emotion | Notebook letters, swimming fish push text apart |
| 19 | 💓 [Heartbeat](heartbeat.html) | Emotion | ECG waveform, Xin Qiji verse burst |
| 20 | 💧 [Tears](tears.html) | Emotion | Rain ↔ vortex dual-mode data art |
| 21 | 🪼 [Jellyfish Lamp](jellyfish.html) | Installation | Watercolor fish chandelier |
| 22 | 🪺 [Dreamcatcher](dreamcatcher.html) | Installation | Pessoa · empty → sprout → bloom |
| 23 | 🪲 [Fireflies](firefly.html) | Nature | Summer night firefly swarm, Du Mu · Nara |
| 24 | ❄️ [Cold Mountain](snow.html) | Poetry | Friedrich · sublime solitude, Zhang Ji |
| 25 | 🪞 [Mirror Blossom](mirror.html) | Installation | Magritte · surreal water reflection |
| 26 | 🫁 [Breath](breath.html) | Emotion | Kapoor · void pulse, heartbeat |

## Tech Stack

- **Canvas 2D** — particle systems, ink wash, physics simulations
- **Three.js** — 3D scenes (sakura, peach, ginkgo, trap tunnel)
- **Web Audio API** — procedural sound for each scene (wind chimes, ocean, guqin, heartbeat…)
- **Single-file HTML** — every scene is one file, no build step, no CDN
- **Mobile-first** — fullscreen canvas, touch events, responsive

## How to Run

```bash
git clone https://github.com/wemamawe/wanxiang.git
cd wanxiang
open home.html
# or any scene directly: open lotus.html
```

No install. No build. Just open.

## i18n

The gallery homepage supports **English** and **中文**, switchable via the EN/中文 toggle in the top-right corner. Language preference is saved in localStorage.

## License

MIT
