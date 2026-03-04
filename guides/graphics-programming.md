# 🎮 Graphics Programming Learning Guide
> Curated from GPVM (Graphics Programming Virtual Meetup)
> Source: https://develop--gpvm-website.netlify.app/resources/
> Saved: 2026-03-03

A structured learning path from zero to serious graphics programmer.

---

## 🟢 Start Here — Beginner Friendly

### 1. Learn OpenGL
**Link:** https://learnopengl.com/
**What it is:** The single best starting point for real-time graphics. Teaches OpenGL AND rendering fundamentals simultaneously — so you leave understanding both the API and the underlying concepts.
**Why it matters:** Even if you end up using Vulkan or WebGPU, this is the mental foundation everything else builds on.

### 2. The Book of Shaders
**Link:** https://thebookofshaders.com/
**What it is:** An artistic introduction to shaders. Covers topics that engineering-focused resources skip — procedural art, noise, patterns, color theory in shaders.
**Why it matters:** Changes how you *think* about GPUs. More creative, less dry.

### 3. Barycentric Coordinates (Interactive)
**Link:** https://observablehq.com/@infowantstobeseen/barycentric-coordinates
**What it is:** Interactive notebook introducing barycentric coordinates — starts with linear interpolation, generalizes to triangles.
**Why it matters:** Core primitive for rasterization. Understanding this unlocks how triangles actually work on screen.

### 4. Tone Mapping
**Link:** https://64.github.io/tonemapping/
**What it is:** Theory of tone mapping + comparison of common operators (Reinhard, ACES, etc.)
**Why it matters:** Every renderer needs to map HDR → display. This is where color science meets real-time graphics.

---

## 🔵 Intermediate — Dive Deeper

### 5. The Graphics Codex
**Link:** https://graphicscodex.com/app/app.html
**What it is:** Free book. Physically-based shading and rendering, coding projects, reference pages.
**Why it matters:** Bridges beginner tutorials and serious PBR/rendering theory.

### 6. VkGuide by Victor Blanco
**Link:** https://vkguide.dev/
**What it is:** Correct, modern Vulkan guide. Goal is to properly understand Vulkan before using it in real projects.
**Why it matters:** Vulkan is the modern low-level graphics API. Most AAA and serious indie games use it. This is the right way to learn it.

### 7. Sascha Willems's How to Vulkan in 2026
**Link:** https://howtovulkan.com
**What it is:** Minimalist Vulkan tutorial for 2026 — gets you started with rasterization using modern, commonly supported features.
**Why it matters:** More concise than VkGuide. Great companion or alternative if VkGuide feels heavy.

### 8. VK_KHR_dynamic_rendering Tutorial
**Link:** https://lesleylai.info/en/vk-khr-dynamic-rendering/
**What it is:** How to use the dynamic_rendering extension — modern Vulkan removes the need for render pass objects.
**Why it matters:** This extension is now standard. Any modern Vulkan code uses it.

### 9. Learn wgpu (Rust + WebGPU)
**Link:** https://sotrh.github.io/learn-wgpu/
**What it is:** Tutorial for the WebGPU API using Rust + wgpu library.
**Why it matters:** WebGPU is the future of portable GPU programming (browser + native). wgpu is Rust's implementation — clean, safe, cross-platform.

### 10. Drawing Lines is Hard
**Link:** https://mattdesl.svbtle.com/drawing-lines-is-hard
**What it is:** Deep dive into GPU line rendering — why it's surprisingly hard, and multiple techniques for 2D/3D triangulated line rendering.
**Why it matters:** A deceptively complex problem. The kind of thing that separates beginners from people who actually understand the pipeline.

---

## 🟣 Ray Tracing Track

### 11. Ray Tracing Gems Series
**Link:** https://www.realtimerendering.com/raytracinggems/
**What it is:** Multi-book series of expert articles on ray tracing techniques. Not introductory — assumes you know the basics.
**Why it matters:** The "advanced topics" book for ray tracing. Written by practitioners, for practitioners.

### 12. vk_mini_path_tracer
**Link:** https://nvpro-samples.github.io/vk_mini_path_tracer
**What it is:** Small, beginner-friendly path tracing tutorial using Vulkan's ray tracing API.
**Why it matters:** Best hands-on intro to hardware ray tracing. NVIDIA-authored, well-maintained.

### 13. NVIDIA Vulkan Ray Tracing Tutorials
**Link:** https://github.com/nvpro-samples/vk_raytracing_tutorial_KHR/
**What it is:** Step-by-step tutorial transforming a rasterization app into a full ray tracer.
**Why it matters:** More comprehensive than vk_mini — shows the full rasterization → ray tracing progression.

### 14. The Ray Tracer Challenge
**Link:** http://raytracerchallenge.com/
**What it is:** Build a Whitted-style ray tracer from scratch using a test-first approach. No code provided — only specs and algorithms.
**Why it matters:** Forces genuine understanding. Language-agnostic. Great for deeply learning the math.

### 15. The Rendering Equation (Kajiya 1986)
**Link:** https://dl.acm.org/doi/10.1145/15886.15902
**What it is:** The original paper introducing the rendering equation and path tracing algorithm.
**Why it matters:** The theoretical foundation of all physically-based rendering. Read this at some point — even if just the intro.

---

## 🔴 Advanced / Specialized

### 16. Dartmouth CS87 — Rendering Algorithms
**Link:** https://cs87-dartmouth.github.io/
**What it is:** Graduate course filling the gap between toy renderers (Ray Tracing in a Weekend) and full production renderers (PBRT).
**Why it matters:** The perfect bridge course. After LearnOpenGL + a toy ray tracer, this is next.

### 17. CMU 15-462/662 — Variance Reduction (Lecture 19)
**Link:** https://youtu.be/IQhLk_XaFc8
**What it is:** Covers bidirectional path tracing, Metropolis-Hastings, MIS, stratified sampling, blue noise, photon mapping, and radiosity.
**Why it matters:** Monte Carlo rendering fundamentals from one of the best graphics courses in the world.

### 18. Percentage-Closer Soft Shadows (PCSS)
**Link:** https://developer.download.nvidia.com/shaderlibrary/docs/shadow_PCSS.pdf
**What it is:** NVIDIA paper introducing PCSS — soft shadows via shadow mapping + percentage-closer filtering.
**Why it matters:** Classic technique still used in games. Good intro to shadow algorithms.

### 19. TinyBVH
**Link:** https://github.com/jbikker/tinybvh
**What it is:** Single-header, zero-dependency BVH construction and traversal library.
**Why it matters:** BVH (Bounding Volume Hierarchy) is the core acceleration structure for ray tracing. This is the minimal, clean implementation to learn from.

### 20. Memory Allocation Strategies Series
**Link:** https://www.gingerbill.org/series/memory-allocation-strategies/
**What it is:** Article series on custom allocators — arena, stack, pool, free list, buddy allocators.
**Why it matters:** Graphics programming at performance level requires custom memory management. This is the foundation.

### 21. Data-Oriented Design
**Link:** https://www.dataorienteddesign.com/site.php
**What it is:** Book on DOD — beyond cache misses and ECS, into fundamentally restructuring programs around data flow.
**Why it matters:** High-performance graphics code is inherently data-oriented. This reframes how you design systems.

### 22. Better Code: Concurrency — Sean Parent (Talk)
**Link:** https://www.youtube.com/watch?v=zULU6Hhp42w
**What it is:** C++14-based task system, issues with mutex/shared data, concurrent queues and thread pools.
**Why it matters:** GPU work happens in parallel. Understanding CPU-side concurrency is essential for feeding the GPU efficiently.

---

## 🎨 Assets & Tools

### 23. ambientCG
**Link:** https://ambientcg.com/
**What it is:** Public domain PBR materials (textures, HDRIs, models).
**Why it matters:** Free, CC0-licensed assets for testing renderers. No attribution required.

### 24. docs.gl
**Link:** http://docs.gl/
**What it is:** Improved OpenGL documentation — better organized and more readable than the official spec.
**Why it matters:** Bookmark this. You'll use it constantly when writing OpenGL code.

### 25. 3D Graphics Rendering Cookbook
**Link:** https://www.amazon.com/Graphics-Rendering-Cookbook-comprehensive-algorithms/dp/1838986197
**What it is:** Book covering both OpenGL and Vulkan with renderer recipes.
**Why it matters:** Practical, hands-on. Good for people who learn by building.

---

## 🗺️ Suggested Learning Path

```
BEGINNER
└── Learn OpenGL → Book of Shaders → Barycentric Coordinates

INTERMEDIATE
├── Graphics Codex (theory)
└── VkGuide / How to Vulkan 2026 (API)
    └── VK_KHR_dynamic_rendering

RAY TRACING
└── vk_mini_path_tracer → NVIDIA RT Tutorials → Ray Tracing Gems

ADVANCED
└── Dartmouth CS87 → CMU 15-462 Variance Reduction → PBRT (when ready)

PERFORMANCE
└── Data-Oriented Design → Memory Allocation Strategies → Sean Parent Concurrency
```

---

*Source saved locally and to research library. Next: add to ai-research-hub/guides/ on GitHub.*
