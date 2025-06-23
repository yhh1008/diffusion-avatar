# 🪄 Magical Girls Generator

> A browser-only diffusion avatar demo — no backend, just WebAssembly & Web Workers!

🔗 **Live demo:** https://yhh1008.github.io/diffusion-avatar/

---

## 🚀 What is this?

“Magical Girls Generator” runs a **reverse diffusion** sampler (DDPM/DDIM) entirely in your browser:

- Starts from pure Gaussian noise  
- Iteratively denoises to reveal cute avatars  
- Exposes an **“Acceleration”** slider so you can trade off speed ↔ image quality via DDIM skips  

All model weights and inference code live in a Web Worker under `public/64x64_cosin_300/`.



