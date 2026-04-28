
# Few-Shot Learning su LIVECell-CLS

Gruppo: Gabriele Pio Bonavita · Christian Criscitiello · Cristian Franzese

---

## Descrizione del progetto

Questo progetto affronta la classificazione di linee cellulari in microscopia a contrasto di fase in regime **Few-Shot**, usando il dataset **LIVECell-CLS**.

L'obiettivo è distinguere nuove linee cellulari (*novel classes*) disponendo di soli 1–5 esempi etichettati per classe, superando il vincolo del Deep Learning tradizionale che richiede un costoso riaddestramento per ogni nuova categoria.

---

## Dataset

Il dataset **LIVECell-CLS** deriva dall'originale LIVECell (Edlund et al., 2021) ed è stato introdotto nel paper:

> *Advancing Label-Free Cell Classification with Connectome-Inspired Explainable Models and a novel LIVECell-CLS Dataset* — Fiore, Terlizzi, Bardozzo, Liò e Tagliaferri (2025)

Le immagini sono state acquisite tramite il sistema **Incucyte S3** di Sartorius e preprocessate rimuovendo lo sfondo con zero-padding uniforme nero.

---

## Architettura

Il sistema è composto da tre componenti principali:

1. **Backbone — ResNet-18** pre-addestrata su ImageNet, usata come feature extractor (output: embedding a 512 dimensioni via Global Average Pooling)
2. **Projection Head** — riduce la dimensionalità a 256 (lineare nella baseline, MLP non lineare nell'Esperimento D)
3. **ProtoNet Head** — classifica calcolando la distanza euclidea minima tra gli embedding e i prototipi di classe

---

## Esperimenti

| Esperimento | Descrizione | Test Accuracy (media) |
|---|---|---|
| **A — Baseline** | ResNet-18 + Linear Head + ProtoNet | ~78% |
| **B — TTA** | Test-Time Augmentation con gruppo diedrale D4 (8 viste) | **79.39%** |
| **C — L2 + Scaling** | Normalizzazione L2 su ipersfera unitaria + scaling factor apprendibile (α ≈ 9.985) | **80.39%** ✅ |
| **D — MLP** | Projection head non lineare (ReLU + Dropout p=0.2) | 79.27% |
| **CLAHE** | Pre-processing con equalizzazione adattiva del contrasto | ❌ Peggioramento |

Il **miglior esperimento** è la combinazione **TTA + L2 normalizzazione + learnable scaling** con un'accuratezza media dell'**80.39%** in setting 5-shot.

---

## Ambiente di lavoro

- **Piattaforma:** Kaggle Kernels
- **Hardware:** 2× GPU NVIDIA Tesla T4 (32 GB VRAM totali)
- **Tecniche di ottimizzazione:** DataParallel, Automatic Mixed Precision (AMP), checkpoint frequenti
- **Budget:** 30 ore settimanali di GPU

🔗 **[Apri il notebook su Kaggle](#)** ← *inserire qui il link al notebook*

---

## Risultati principali

- Accuratezza media dell'**80.39%** in 5-shot su 2 classi novel, contro il 50% della random guess
- La **TTA con D4** è risultata critica negli scenari 1-shot (+1.5% di accuracy)
- La **normalizzazione L2 con scaling** ha migliorato il Silhouette Score UMAP da 0.55 a 0.65
- La **non-linearità dell'MLP** non ha portato benefici rispetto alla projection head lineare
- La **CLAHE** ha peggiorato le performance: le feature di texture originali sono più discriminanti di quelle artificialmente contrastate

---

## Sviluppi futuri

- **Partial Convolutions (P-Conv):** mascherare attivamente le aree di zero-padding per migliorare la qualità degli embedding
- **Ampliamento del dataset:** aggiungere linee cellulari eterogenee per aumentare la variabilità episodica e ridurre l'overfitting sulle classi base

---

## Riferimenti

- He et al. (2015) — *Deep Residual Learning for Image Recognition*
- Snell et al. (2017) — *Prototypical Networks for Few-shot Learning*
- Edlund et al. (2021) — *LIVECell — A large-scale dataset for label-free live cell segmentation*
- Fiore et al. (2025) — *Advancing Label-Free Cell Classification with Connectome-Inspired Explainable Models and a novel LIVECell-CLS Dataset*
- Liu et al. (2018) — *Partial Convolution based Padding*
