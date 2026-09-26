# Gio Kiefer A. Sanchez

Computer vision belongs where the camera is: on a laptop, in a browser tab, or inside an edge box. I build lightweight, task-specific models engineered for strict hardware constraints. I measure before I trust a result, and I assume the benchmark is flawed until the data proves otherwise.

Three recent projects. The last two run in your browser, and neither needs a server.

| | Size | Speed | |
| :--- | :--- | :--- | :--- |
| **Multi-person tracker**<br>Learned matcher with a memory bank. | 5,700-parameter matcher | 33 fps (whole pipeline, RTX 4050) | **[Code](https://github.com/giosanchez0208/Retrieval-Augmented-Association)**  |
| **FLEA**<br>Reads room lighting from one frame. | 4.1 MB | 2.1 ms (1 CPU core) / 8.5 ms (browser) | **[Try it](https://huggingface.co/spaces/giosanchez0208/fast-lighting-estimation-for-ar)** |
| **Clothing color**<br>Distilled until it beat its teacher. | 4.2 MB INT8 | 6.9 ms (CPU) | **[Try it](https://huggingface.co/spaces/giosanchez0208/clothing-color-recognition)** |

---

## Multi-person tracking: a small matcher with a memory
A real-time tracker for a fixed camera (e.g., CCTV inside a bus). A 5,700-parameter matcher decides which new box belongs to which known person, and a memory bank of everyone seen lets it re-identify people after occlusions. On MOT17's validation half, given identical boxes:

| Tracker | HOTA | IDF1 | ID switches |
| :--- | :--- | :--- | :--- |
| SORT | 48.4 | 54.5 | 222 |
| ByteTrack | 49.4 | 56.2 | 198 |
| DeepSORT | 51.9 | 60.6 | 102 |
| **Mine** (avg of 6 trained matchers) | **52.6** | **61.7** | **98.3** |

With my fine-tuned detector, the entire pipeline hits **54.3 HOTA at 33 fps** on a laptop RTX 4050. Every model was trained locally on that same machine.

*   **Same loader, same scorer, same boxes.** I reimplemented SORT, ByteTrack, and DeepSORT from their papers to isolate the matching logic. Before trusting the scorer, I verified it gives ground truth 100 and untracked detections 5.0 HOTA.
*   **A better appearance model made tracking worse.** DeepSORT went from 121 to 145 ID switches because its fixed cutoff didn't fit the new distances. Two boxes in the same frame are strictly distinct people, so I recalibrated the cutoff from those unlabeled pairs. ID switches dropped to 98.
*   **The heavy ideas lost.** Attention across candidates scored 94.6% precision on held-out pairs (beating the simple matcher's 89.7%), but spiked ID switches to 379 inside the tracker. The most effective cue was the simplest: overlap with where the person was last matched (cut ID switches from 107 to 98.3).

*Where it falls short:* The lead over DeepSORT holds on average, not on every single run (individual matchers land between 95 and 108 ID switches vs. DeepSORT's 102). Occluded heads and shoulders drop recognition from 82.3 to 35.4 mAP. Untested on cameras outside MOT17.
*Context:* Built on a COIL presentation with Mark Gallardo and Caine Bautista.
[Code and write-up →](https://github.com/giosanchez0208/Retrieval-Augmented-Association)

---

## FLEA: lighting for AR in twelve numbers
A virtual object looks pasted on when its light doesn't match the room. FLEA processes a single frame and returns the three lights a game engine already expects: flood, key, and back. Twelve numbers total.

*   **Zero measured lighting labels.** Trained on 100,604 rendered frames from 1,383 3D models, plus 7,105 real photos labeled by an earlier version of the model.
*   **The pass bar came first.** Before training, I verified three lights could explain 85% of real HDR shading, or the rig would require a fourth. Fitted directly, they explained 91.4% across 192 fits.
*   **Every result sits next to the best constant.** On real phone photos, FLEA reproduces 52.2% of a real ball's shading. The best single guess ignoring the image manages 29.2%. The ceiling is 85.2%.

*Where it falls short:* It loses to the constant on 1 out of 10 real photos. On held-out rendered scenes, its key-light direction is off by 32° on average with the object centered, and 38° with objects anywhere in frame.
[Write-up →](https://github.com/giosanchez0208/Fast-Lighting-Estimation-For-AR)

---

## Clothing color: the student beat the teacher

| | Student (4.2 MB INT8) | Teacher (ResNet-50) |
| :--- | :--- | :--- |
| **KL divergence** | **0.480** | 0.623 |
| **Top-1** | **67.4%** | 63.2% |
| **CPU latency** | **6.9 ms** | 45 ms |

*21× smaller, 6.6× faster, and strictly better on all 13 classes.*

People disagree on where blue ends and teal begins; assigning a single label throws that uncertainty away. I predicted the distribution instead, generating all 28,860 training images procedurally (Perlin folds, 7 patterns, 10 augmentation axes) so every label distribution is known by construction.
[Code and write-up →](https://github.com/giosanchez0208/Clothing-Color-Recognition-ML-With-Synthetic-Dataset)

### What my own tests told me I'd got wrong
A paired one-factor-at-a-time probe set isolated what each augmentation cost. It exposed 8 defects. Two were flaws in my own experimental design. 

It also proved the model had implicitly learned color constancy in its weights, so I stripped white-balancing from the inference path. I had been paying for it on every frame for nothing. 

---

## Published
**[A Parallelized Agent-Based Optimization Framework for Jeepney Routes with Integrated Passenger Behavior Simulation](https://doi.org/10.1109/CyberSciTech68397.2025.00038)**
First author · IEEE CyberSciTech 2025 · Hakodate
 
Jeepney routes grow out of habit and negotiation. I modeled the passengers as well as the vehicles: behavior calibrated from a 214-response bilingual commuter survey, converted into Equivalent In-Vehicle Minutes, then optimized with a hybrid GA-ACO memetic search. Simulated commute times fell 15 to 25%.
 
[Code →](https://github.com/giosanchez0208/Jeepney-Route-System-Optimization)

## Shipped
Four production CV models running concurrently on edge hardware: lightweight ALPR, a Philippine-vehicle classifier, clothing-color recognition, and 3D-augmented face matching with vector retrieval. Operating at sub-20 ms per model (15 FPS end-to-end) across multiple field stations of a municipal surveillance pilot.

* The robustness harness I built revealed the plate reader was robust to blur but broke on sharpening—the exact opposite of our assumption. We patched the training data.

**Constraints I like working under:** It runs on the device, it holds up when someone tries to break it, and it explicitly states what it doesn't know.

---

**ML/CV:** PyTorch · ONNX Runtime (Web) · OpenCV · Transformers · Distillation · INT8 Quantization · Edge/In-browser Inference · Synthetic Data

**Simulation/Math:** OSMnx · NetworkX · GeoPandas · SciPy · Agent-based Simulation · GA/ACO Metaheuristics

**Languages/DB:** Python · C++ · Java · SQL · JavaScript

**Bio:** BS Computer Science, Magna Cum Laude, MSU-IIT, 2026 · Philippines (GMT+8)

[giokiefersanchez@gmail.com](mailto:giokiefersanchez@gmail.com) · [LinkedIn](https://www.linkedin.com/in/giosanchez0208) · [Hugging Face](https://huggingface.co/giosanchez0208)
