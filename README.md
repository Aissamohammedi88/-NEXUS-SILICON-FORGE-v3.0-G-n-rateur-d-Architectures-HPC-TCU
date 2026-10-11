# -NEXUS-SILICON-FORGE-v3.0-G-n-rateur-d-Architectures-HPC-TCU
Avec le preset H100-like (132 SM), le forge génère **~3000–4500 lignes de SystemVerilog** réparties sur 12 fichiers hiérarchiques : top → clusters → SMs → tensor cores → NoC → HBM → PCIe → DVFS → security → testbench → SDC → floorplan Tcl → Makefile → spec Markdown. Le nombre de lignes *explose proportionnellement* à 
