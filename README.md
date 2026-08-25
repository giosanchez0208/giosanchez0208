# Gio Kiefer A. Sanchez

Computer vision, at the small end: edge inference, model compression, synthetic data.

I distilled a ResNet-50 into a **4.2 MB** model that beats it on all 13 categories. It runs in your browser: **[try it](https://huggingface.co/spaces/giosanchez0208/clothing-color-recognition)**, it'll finish loading before you finish this page.

| | Student, 4.2 MB INT8 | Teacher, ResNet-50 |
|---|---|---|
| KL divergence | **0.480** | 0.623 |
| Top-1 | **67.4%** | 63.2% |
| CPU latency | **6.9 ms** | 45 ms |

21× smaller. 6.6× faster. Better on every class.

---

### The interesting part wasn't the compression

People genuinely disagree about where blue ends and teal begins. So a single label is the wrong target. The ambiguity is exactly what we should be looking at.

I predicted the **distribution** instead, and generated all 28,860 training images procedurally (with Perlin folds, 7 patterns, 10 augmentation axes) so the label distributions were known by construction rather than estimated from annotators who wouldn't agree on the same color anyway.

[Code and write-up →](https://github.com/giosanchez0208/Clothing-Color-Recognition-ML-With-Synthetic-Dataset)

### What my own tests told me I'd got wrong

I built a paired one-factor-at-a-time probe set to isolate what each augmentation actually cost. It found 8 defects. **Two of them were in the experimental design itself.**

It also showed the model had learned color constancy in-weights, which meant I could delete white-balancing from the inference path entirely. I'd been paying for it every frame for no reason.

I'd rather find these myself than have production find them for me.

---

### Published

**[A Parallelized Agent-Based Optimization Framework for Jeepney Routes with Integrated Passenger Behavior Simulation](https://doi.org/10.1109/CyberSciTech68397.2025.00038)**
First author · IEEE CyberSciTech 2025 · Hakodate

Jeepney routes are set by habit and negotiation, not by design. I modelled the passengers rather than just the vehicles — behaviour calibrated from a 214-response bilingual commuter survey, converted into Equivalent In-Vehicle Minutes — then optimised over it with a hybrid GA-ACO memetic search. Simulated commute times fell 15–25%.

[Code →](https://github.com/giosanchez0208/Jeepney-Route-System-Optimization)

### Shipped

Four production CV models running **concurrently** on edge hardware: lightweight ALPR, Philippine-vehicle classifier, clothing-colour recognition, and 3D-augmented face matching with vector retrieval. Sub-20 ms per model, 15 FPS end-to-end, supporting a live municipal surveillance pilot across multiple field stations.

Constraints I like working under: it has to run on the device, it has to hold up when someone tries to break it, and it has to be honest about what it doesn't know.

---

**PyTorch · ONNX Runtime · OpenCV · Transformers** — distillation, INT8 quantization, edge inference, synthetic data

**OSMnx · NetworkX · GeoPandas · SciPy** — agent-based simulation, GA/ACO metaheuristics

**Python · C++ · Java · SQL · JavaScript**

BS Computer Science, *Magna Cum Laude* — MSU-IIT, 2026

[giokiefersanchez@gmail.com](mailto:giokiefersanchez@gmail.com) · [LinkedIn](https://www.linkedin.com/in/giosanchez0208) · [Hugging Face](https://huggingface.co/giosanchez0208)
