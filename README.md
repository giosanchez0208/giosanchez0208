# Gio Kiefer A. Sanchez

Computer vision at the small end: edge inference, model compression, synthetic data. I build the measurement before the model, and I assume the measurement is wrong until it proves otherwise.

Two of my models run in your browser. Neither needs a server.

| | Size | Speed | |
|---|---|---|---|
| **FLEA** reads a room's lighting from one camera frame | 4.1 MB | 2.1 ms on one CPU core · 8.5 ms in a browser tab | **[Try it](https://huggingface.co/spaces/giosanchez0208/fast-lighting-estimation-for-ar)** |
| **Clothing colour**, distilled until it beat its own teacher | 4.2 MB INT8 | 6.9 ms on CPU | **[Try it](https://huggingface.co/spaces/giosanchez0208/clothing-color-recognition)** |

---

### FLEA: lighting for AR in twelve numbers

A virtual object looks pasted on when its light doesn't match the room. FLEA looks at one frame and returns the three lights a game engine already has: a flood, a key and a back light. Twelve numbers.

- **Zero measured lighting labels.** 100,604 rendered frames from 1,383 3D models, plus 7,105 real photos labelled by an earlier version of the model.
- **The pass bar came first.** Before training, three lights had to explain 85% of real HDR shading, or the rig would get a fourth. Fitted directly, they explained 91.4% across 192 fits.
- **Every result sits next to the best constant.** On real phone photos, FLEA reproduces 52.2% of a real ball's shading. The best single guess that ignores the image manages 29.2%. The ceiling is 85.2%.

Where it falls short: it loses to the constant on 1 of 10 real photos, and on held-out rendered scenes its key-light direction is off by 32° on average with the object centred, 38° with objects anywhere in frame.

[Write-up →](https://github.com/giosanchez0208/Fast-Lighting-Estimation-For-AR)

### Clothing colour: the student beat the teacher

| | Student, 4.2 MB INT8 | Teacher, ResNet-50 |
|---|---|---|
| KL divergence | **0.480** | 0.623 |
| Top-1 | **67.4%** | 63.2% |
| CPU latency | **6.9 ms** | 45 ms |

21× smaller, 6.6× faster, and better on all 13 classes.

People disagree about where blue ends and teal begins, so a single label throws that disagreement away. I predicted the distribution instead, and generated all 28,860 training images procedurally (Perlin folds, 7 patterns, 10 augmentation axes) so every label distribution is known by construction.

[Code and write-up →](https://github.com/giosanchez0208/Clothing-Color-Recognition-ML-With-Synthetic-Dataset)

### What my own tests told me I'd got wrong

A paired one-factor-at-a-time probe set isolated what each augmentation cost. It found 8 defects. **Two were in my experimental design.**

It also showed the model had learned colour constancy in its weights, so I deleted white-balancing from the inference path. I had been paying for it on every frame.

I'd rather find these myself than have production find them for me.

---

### Published

**[A Parallelized Agent-Based Optimization Framework for Jeepney Routes with Integrated Passenger Behavior Simulation](https://doi.org/10.1109/CyberSciTech68397.2025.00038)**
First author · IEEE CyberSciTech 2025 · Hakodate

Jeepney routes grow out of habit and negotiation. I modelled the passengers as well as the vehicles: behaviour calibrated from a 214-response bilingual commuter survey, converted into Equivalent In-Vehicle Minutes, then optimised with a hybrid GA-ACO memetic search. Simulated commute times fell 15 to 25%.

[Code →](https://github.com/giosanchez0208/Jeepney-Route-System-Optimization)

### Shipped

Four production CV models running **concurrently** on edge hardware: lightweight ALPR, a Philippine-vehicle classifier, clothing-colour recognition, and 3D-augmented face matching with vector retrieval. Sub-20 ms per model, 15 FPS end to end, across multiple field stations of a municipal surveillance pilot.

The robustness harness I built there found the plate reader was robust to blur and broke on sharpening, the opposite of what we assumed. We changed the training data.

Constraints I like working under: it runs on the device, it holds up when someone tries to break it, and it says what it doesn't know.

---

**PyTorch · ONNX Runtime · ONNX Runtime Web · OpenCV · Transformers** · distillation, INT8 quantization, edge and in-browser inference, synthetic data

**OSMnx · NetworkX · GeoPandas · SciPy** · agent-based simulation, GA/ACO metaheuristics

**Python · C++ · Java · SQL · JavaScript**

BS Computer Science, *Magna Cum Laude*, MSU-IIT, 2026 · Philippines, GMT+8

[giokiefersanchez@gmail.com](mailto:giokiefersanchez@gmail.com) · [LinkedIn](https://www.linkedin.com/in/giosanchez0208) · [Hugging Face](https://huggingface.co/giosanchez0208)
