# LaTeX Installation on Arch Linux

## Option 1: Full Installation (Recommended)
Install everything you'll likely need:

```bash
sudo pacman -S texlive-latex texlive-latexextra texlive-bibtexextra texlive-binextra texlive-langeuropean biber
```

## Option 2: Minimal Installation
Install only essential packages:

```bash
sudo pacman -S texlive-basic texlive-latex texlive-latexextra texlive-bibtexextra biber
```

## Testing the Installation

```bash
cd /home/ei/uni/7-semester/bachelor/report
pdflatex main.tex
biber main
pdflatex main.tex
pdflatex main.tex
```

Or with latexmk (included in texlive-core):
```bash
latexmk -pdf main.tex
```

## What Each Package Provides

- `texlive-basic`: Core LaTeX system
- `texlive-latex`: Standard LaTeX packages
- `texlive-latexextra`: Extra packages (geometry, fancyhdr, listings, tikz, etc.)
- `texlive-bibtexextra`: Bibliography tools (biblatex)
- `texlive-binextra`: Auxiliary programs including **latexmk**
- `texlive-langeuropean`: European language support (includes Danish babel)
- `biber`: Modern bibliography processor for biblatex

## Troubleshooting

If you get missing package errors, you can install the full texlive group:
```bash
sudo pacman -S texlive
```
This installs all texlive packages (~4 GB).
