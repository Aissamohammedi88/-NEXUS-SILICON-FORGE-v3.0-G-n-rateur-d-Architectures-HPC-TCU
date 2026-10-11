# -NEXUS-SILICON-FORGE-v3.0-G-n-rateur-d-Architectures-HPC-TCU
Avec le preset H100-like (132 SM), le forge génère **~3000–4500 lignes de SystemVerilog** réparties sur 12 fichiers hiérarchiques : top → clusters → SMs → tensor cores → NoC → HBM → PCIe → DVFS → security → testbench → SDC → floorplan Tcl → Makefile → spec Markdown. Le nombre de lignes *explose proportionnellement* à 
```markdown
# ⚡ NEXUS SILICON STUDIO

<p align="center">
<img src="https://img.shields.io/badge/version-3.0.0-c8a45c?style=for-the-badge&labelColor=0d1117" />
<img src="https://img.shields.io/badge/python-3.8%2B-00d4ff?style=for-the-badge&labelColor=0d1117" />
<img src="https://img.shields.io/badge/verilog-synthétisable-4ade80?style=for-the-badge&labelColor=0d1117" />
<img src="https://img.shields.io/badge/license-NEXUS--OPEN--3.0-ff6ec7?style=for-the-badge&labelColor=0d1117" />
<img src="https://img.shields.io/badge/dependencies-ZERO-9d4edd?style=for-the-badge&labelColor=0d1117" />
</p>

<p align="center">
<b>Vrai calculateur d'architecture silicium · Générateur RTL Verilog · Web Dashboard</b><br>
<i>Conçu par Aissa Mohammedi (DGK) — Port 9982</i>
</p>

---

## 🌟 Vue d'ensemble

**NEXUS SILICON STUDIO** est un studio complet d'exploration d'architectures HPC accélérateurs IA. Il combine :

- **Un calculateur physique** basé sur des formules réelles (P = αCV²f, Yield binomial, Roofline, thermique)
- **Un générateur RTL Verilog-2001 synthétisable** (testé avec iverilog et yosys)
- **Un dashboard web interactif** (port 9982)
- **Zéro dépendance externe** (Python stdlib uniquement)

Contrairement aux simulateurs "boîte noire", **chaque chiffre est justifié par une formule nommée**, une source, et une unité.

---

## 🎯 Objectif

Permettre à un ingénieur ou un chercheur de :

1. **Calculer** les caractéristiques d'une puce accélérateur (TFLOPS, BW, TDP, die area, yield, coût)
2. **Générer** le RTL Verilog correspondant, prêt à simuler et synthétiser
3. **Comparer** aux références NVIDIA H100/B200, AMD MI300X, Google TPU
4. **Identifier** les goulots d'étranglement réels (compute-bound, memory-bound, thermique, yield)

---

## 🚀 Installation

### Prérequis

- Python 3.8+
- (Optionnel) `iverilog` + `vvp` pour la simulation
- (Optionnel) `yosys` pour la synthèse
- (Optionnel) `gtkwave` pour visualiser les waveforms

### Installation rapide

```bash
git clone https://github.com/Aissamohammedi88/nexus-silicon-studio.git
cd nexus-silicon-studio
python3 silicon_studio.py
```

Ouvre ensuite : http://localhost:9982

Installation des outils RTL (optionnel)

```bash
# Debian / Ubuntu / Linux Mint
sudo apt install iverilog yosys gtkwave

# macOS (Homebrew)
brew install icarus-verilog yosys gtkwave
```

---

📖 Utilisation

1. Lancer le serveur

```bash
python3 silicon_studio.py
```

Le serveur démarre sur http://localhost:9982 et détecte automatiquement un dossier inscriptible (compatible iOS / Termux).

2. Configurer les axiomes matériels

Ouvre http://localhost:9982 dans ton navigateur et remplis :

Paramètre Description Exemple
SM count Nombre de Streaming Multiprocessors 132
Fréquence Fréquence cœur en MHz 1800
FMA / SM / cycle Opérations FMA par SM par cycle 128
Tensor ratio Multiplicateur Tensor Core 4.0
HBM stacks Nombre de piles HBM 5
HBM channels Canaux par pile 16
HBM width Largeur d'un canal (bits) 64
HBM GT/s Débit HBM 6.4
Process Nœud technologique (nm) 4
TDP Enveloppe thermique (W) 700

3. Lire les résultats

Le calculateur affiche :

· Technologie : densité, tension, coût wafer, fréquence max
· Transistors : total, par SM, par bloc
· Die : surface, dies/wafer, yield, coût/die
· Calcul : TFLOPS FP16/FP8/INT8, sparse
· Mémoire : BW, capacité, énergie/bit
· Puissance : P_dyn, P_stat, P_memory, P_IO
· Thermique : T_junction, refroidissement requis
· Efficacité : TFLOPS/W, énergie/opération
· Roofline : ridge, régime (memory/compute/balanced)
· Bottlenecks : identifiés honnêtement
· Comparaison : delta vs H100, B200, MI300X, TPU v5e

4. Générer le RTL Verilog

Clique sur "Générer le projet RTL complet". Le studio crée :

```
projects/<chip_name>/
├── rtl/
│ ├── <chip>_top.v
│ ├── <chip>_cluster.v
│ ├── <chip>_hbm_ctrl.v
│ └── <chip>_pcie.v
├── tb/
│ └── tb_<chip>_top.v
├── Makefile
├── README.md
├── model.json
└── config.json
```

5. Simuler et synthétiser

```bash
cd projects/<chip_name>
make sim # iverilog + vvp
make synth # yosys
make wave # gtkwave VCD
```

Ou depuis le dashboard : boutons "Simuler" et "Synthèse".

---

🔬 Formules utilisées

Calcul

Formule Description
TFLOPS = 2 × FMA × SM × freq_GHz × tensor_ratio Convention FMA = 2 opérations
TFLOPS_FP8 = 2 × TFLOPS_FP16 Registre 2× plus large

Mémoire

Formule Description
BW = stacks × channels × width_bits × GT/s ÷ 8 Bande passante HBM
Ridge = FLOPS / BW_bytes Roofline (Williams 2009)

Puissance

Formule Description
P_dyn = α × C × V² × f Puissance dynamique CMOS
P_stat = I_leak × V Puissance statique
T_j = T_a + P × R_th Température junction

Rendement et coût

Formule Description
Y = (1 + A × D₀ / n)^(-n) Yield binomial négatif (Murphy)
N = π(D/2)² / A − πD / √(2A) Dies par wafer
C_die = C_wafer / (N × Y) Coût par die

Densité

Nœud MTr/mm² Vdd (V)
7 nm 90 0.75
5 nm 138 0.70
4 nm 180 0.65
3 nm 240 0.60
2 nm 350 0.55

Sources : rapports publics TSMC, Samsung, Intel (IITC, ISSCC).

---

📚 Références comparées

Référence Année Process FP16 TFLOPS BW GB/s TDP W
NVIDIA A100 SXM4 2020 7 nm 312 2039 400
NVIDIA H100 SXM5 2022 4 nm 989 3350 700
NVIDIA B200 SXM 2024 4 nm 2250 8000 1000
AMD Instinct MI300X 2023 5 nm 1307 5300 750
Google TPU v5e 2023 7 nm 197 819 220

Sources : documentations officielles NVIDIA, AMD, Google Cloud.

---

🏗️ Architecture du projet

```
nexus-silicon-studio/
├── silicon_studio.py # Fichier principal (~2100 lignes)
├── README.md # Ce fichier
├── LICENSE # NEXUS-OPEN-3.0
├── .gitignore # Exclusions Git
└── projects/ # Projets RTL générés (créé au runtime)
└── <chip_name>/
├── rtl/ # RTL Verilog
├── tb/ # Testbench
├── Makefile
├── model.json
└── config.json
```

---

🧠 Philosophie

Chaque chiffre a une formule. Chaque formule a une source. Chaque source est publique.

Pas de score magique. Pas de constante inventée. Pas de "IA-powered" flou.

Si un chiffre ne peut pas être justifié par la physique des semi-conducteurs, il n'est pas affiché.

---

🤝 Contribuer

1. Fork le repo
2. Créer une branche (git checkout -b feature/amelioration)
3. Commit (git commit -m 'Ajout de X')
4. Push (git push origin feature/amelioration)
5. Ouvrir une Pull Request

Idées de contribution

□ Support VHDL additionnel
□ Ajout de nœuds technologiques (1 nm, 18A, 14A)
□ Modèle de coût NRE ASIC
□ Support FPGA (Xilinx, Intel)
□ Interface CLI (sans web)
□ Export PDF des rapports
□ Comparaison avec chips neuromorphiques (Loihi, TrueNorth)
□ Modèle de latence mémoire (cycle-accurate)

---

📜 Licence

NEXUS-OPEN-3.0 — Voir LICENSE

Copyright © 2026 Aissa Mohammedi (DGK)

Permission est accordée, gratuitement, à toute personne obtenant une copie de ce logiciel et des fichiers de documentation associés, de traiter le Logiciel sans restriction, y compris, sans limitation, les droits d'utiliser, copier, modifier, fusionner, publier, distribuer, sous-licencier et/ou vendre des copies du Logiciel.

---

👤 Auteur

Aissa Mohammedi (DGK)

· Protocole : Code Nametron 3 Lara
· Localisation : Québec, Canada
· Année : 2026

---

🙏 Remerciements

· NVIDIA, AMD, Google pour les spécifications publiques
· Yosys et Icarus Verilog pour les outils open source
· La communauté open source pour l'inspiration

---

<p align="center">
<b>⚡ NEXUS SILICON STUDIO</b><br>
<i>Formules réelles. RTL synthétisable. Zéro dépendance.</i>
</p>
```Sauvegarde : Ctrl+O, Entrée, Ctrl+X.

---

ÉTAPE 3 — Publier sur GitHub

```bash
cd ~/nexus_silicon

# Initialiser le dépôt git
git init

# Ajouter tous les fichiers
git add .

# Vérifier ce qui va être commité
git status

# Commit initial
git commit -m "Initial commit — NEXUS SILICON STUDIO v3.0.0"

# Ajouter le remote GitHub (remplace par ton URL)
git remote add origin https://github.com/Aissamohammedi88/nexus-silicon-studio.git

# Push
git branch -M main
git push -u origin main
```

---

ÉTAPE 4 — Vérifier

Ouvre ton repo GitHub. Tu dois voir :

· silicon_studio.py
· README.md
· .gitignore
· Le README s'affiche automatiquement en page d'accueil avec les badges

Si GitHub demande un token pour le push :

1. Va sur https://github.com/settings/tokens
2. Generate new token (classic)
3. Coche repo (accès complet aux repos)
4. Copie le token
5. Au moment du git push, utilise le token comme mot de passe
