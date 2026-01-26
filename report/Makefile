# LaTeX Makefile with hot-reload and build commands

# Configuration
MAIN = main
OUTPUT = BA_Report
BUILD_DIR = build
PDF = $(BUILD_DIR)/$(OUTPUT).pdf

# Default target
.PHONY: all
all: build

# Hot-reload mode for writing (continuous compilation)
.PHONY: watch
watch:
	@mkdir -p $(BUILD_DIR)
	latexmk -pdf -pvc -outdir=$(BUILD_DIR) -jobname=$(OUTPUT) $(MAIN).tex

# One-time compilation to PDF
.PHONY: build
build:
	@mkdir -p $(BUILD_DIR)
	latexmk -pdf -outdir=$(BUILD_DIR) -jobname=$(OUTPUT) $(MAIN).tex

# Clean build artifacts
.PHONY: clean
clean:
	latexmk -C -outdir=$(BUILD_DIR)
	rm -rf $(BUILD_DIR)

# Clean and rebuild
.PHONY: rebuild
rebuild: clean build

# Show the PDF location
.PHONY: show
show:
	@echo "PDF location: $(PDF)"

# Help message
.PHONY: help
help:
	@echo "Available targets:"
	@echo "  make watch   - Start hot-reload mode (continuous compilation)"
	@echo "  make build   - Compile document once"
	@echo "  make clean   - Remove all build files"
	@echo "  make rebuild - Clean and rebuild from scratch"
	@echo "  make show    - Show PDF location"
	@echo "  make help    - Show this help message"
