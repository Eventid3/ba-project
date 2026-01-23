# Bachelor Thesis Report

This is a LaTeX-based bachelor thesis report structure.

## Building the PDF

### Prerequisites
You need a LaTeX distribution installed:
- **Linux**: `sudo apt install texlive-full biber`
- **macOS**: Install MacTeX
- **Windows**: Install MiKTeX or TeX Live

### Build commands

```bash
# Build the PDF
pdflatex main.tex
biber main
pdflatex main.tex
pdflatex main.tex

# Or use latexmk (recommended)
latexmk -pdf main.tex

# Clean temporary files
latexmk -c
```

## Structure

- `main.tex` - Main document with imports and configuration
- `chapters/` - Individual chapter files
- `figures/` - Directory for images and diagrams (create this folder when needed)
- `references.bib` - Bibliography database

## Adding figures

Place your images in a `figures/` folder and include them like:

```latex
\begin{figure}[H]
    \centering
    \includegraphics[width=0.8\textwidth]{your_image.png}
    \caption{Your caption}
    \label{fig:your_label}
\end{figure}
```

## Adding references

Add entries to `references.bib` and cite them in text with `\cite{key}`.
