# Acessilia Dataset

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Test documents and expected extraction outputs for the [Acessilia](https://github.com/A11yDevs/acessilia) ecosystem.

## Overview

**Acessilia Dataset** provides a shared collection of test documents, intermediate processing artifacts, and expected accessible outputs for projects in the Acessilia ecosystem, including:

- [acessilia-structure-extractor](https://github.com/A11yDevs/acessilia-structure-extractor) — document structure extraction service
- [acessilia](https://github.com/A11yDevs/acessilia) — main accessibility processing platform

By maintaining documents, intermediate artifacts, and expected outputs in a single repository, all consuming projects can validate their extraction and accessibility pipelines against the same reference data, ensuring consistent behavior across the ecosystem.

## Repository Structure

```text
acessilia-dataset/
├── README.md
├── LICENSE
├── input/                           # Source documents (numbered sequentially)
│   ├── manifest.csv                 # Metadata per document
│   ├── 001.pdf                      # java-oo-3pgs (tutorial Java OO)
│   ├── 002.pdf                      # java-oo-369pgs (tutorial Java OO completo)
│   ├── 003.pdf                      # java-oo-caps-9-11-39pgs (capítulos 9-11)
│   ├── 004.pdf                      # java-oo-tables-pg26 (tabelas)
│   ├── 005.jpeg                     # sunset-skyline (fotografia)
│   ├── 006.pdf                      # grandezas-e-medidas-42pgs (apostila matemática)
│   ├── 007.pdf                      # grandezas-e-medidas-pg3-42 (fórmulas)
│   └── 008.pdf                      # grandezas-e-medidas-pg7-42 (tabela)
├── intermediate/                    # Intermediate processing artifacts
│   ├── manifest.csv                 # Maps input_id → intermediate files
│   ├── processing-manifest/         # ProcessingManifest JSON snapshots
│   │   ├── 001.json
│   │   └── ...
│   ├── canonical-document/          # Canonical document (future)
│   │   ├── 001.json
│   │   └── ...
│   └── pddl-plan/                   # PDDL planning artifacts (future)
│       ├── 001.json
│       └── ...
└── outputs/                         # Expected accessible outputs
    ├── manifest.csv                 # Maps input_id → output files
    ├── txt/                         # Plain text
    │   ├── 001.txt
    │   └── ...
    ├── html/                        # Accessible HTML
    │   ├── 001.html
    │   └── ...
    ├── pdf/                         # PDF
    │   ├── 001.pdf
    │   └── ...
    ├── pdf_ua/                      # PDF/UA (accessible PDF)
    │   ├── 001.pdf
    │   └── ...
    ├── mp3/                         # Audio (text-to-speech)
    │   ├── 001.mp3
    │   └── ...
    └── epub/                        # EPUB
        ├── 001.epub
        └── ...
```

## Input Documents

| ID | Original filename | Format | Pages | Language | Domain | Notes |
|---|---|---|---|---|---|---|
| `001` | `java-oo-3pgs.pdf` | pdf | 3 | pt-BR | programming | Java OO tutorial |
| `002` | `java-oo-369pgs.pdf` | pdf | 369 | pt-BR | programming | Complete Java OO tutorial |
| `003` | `java-oo-caps-9-11-39pgs.pdf` | pdf | 39 | pt-BR | programming | Chapters 9-11 |
| `004` | `java-oo-tables-pg26.pdf` | pdf | 1 | pt-BR | programming | Example tables |
| `005` | `sunset-skyline.jpeg` | image | 1 | pt-BR | general | Photograph |
| `006` | `grandezas-e-medidas-42pgs.pdf` | pdf | 42 | pt-BR | chemistry | Presentation |
| `007` | `grandezas-e-medidas-pg3-42.pdf` | pdf | 1 | pt-BR | chemistry | Tables |
| `008` | `grandezas-e-medidas-pg7-42.pdf` | pdf | 1 | pt-BR | chemistry | Bar chart |

## Manifests (CSV)

Each top-level directory contains a `manifest.csv` that serves as the index:

- **`input/manifest.csv`** — metadata per source document (id, original filename, format, media type, byte size, pages, language, domain, tables, formulas, images, callouts, chapters, notes)
- **`intermediate/manifest.csv`** — maps `input_id` → intermediate artifacts (processing-manifest, canonical-document, pddl-plan) with extractor version and configuration
- **`outputs/manifest.csv`** — maps `input_id` → output files per format (txt, html, pdf, pdf_ua, mp3, epub) with generator version

## Usage

### As a git submodule

```bash
# In your project
git submodule add https://github.com/A11yDevs/acessilia-dataset.git tests/dataset
```

Then reference documents and expected outputs relative to the submodule path:

```python
from pathlib import Path
import csv

DATASET_DIR = Path("tests/dataset")


def get_inputs() -> list[Path]:
    return sorted(DATASET_DIR.glob("input/*"))


def get_manifest(path: str) -> list[dict]:
    """Load a manifest.csv as a list of dicts."""
    with (DATASET_DIR / path).open(encoding="utf-8") as f:
        return list(csv.DictReader(f))
```

### As a Python package (optional)

```bash
pip install acessilia-dataset@git+https://github.com/A11yDevs/acessilia-dataset.git
```

```python
from acessilia_dataset import get_inputs_dir, get_intermediate_dir, get_outputs_dir

for doc in get_inputs_dir().rglob("*"):
    print(doc)
```

## Contributing

Contributions of new test documents are welcome. Please follow these guidelines:

1. Add the source document to `input/` with the next sequential number (`009`, `010`, …)
2. Record its metadata in `input/manifest.csv`
3. Generate the intermediate artifacts and outputs using the reference pipeline
4. Ensure the document is small (prefer under 1 MB) and does not contain copyrighted material unless properly licensed
5. Submit a pull request

## Relationship with Acessilia

This repository is part of the [Acessilia](https://github.com/A11yDevs/acessilia) ecosystem. It provides shared test infrastructure for:

- **acessilia** — main accessibility processing platform
- **acessilia-structure-extractor** — standalone document structure extraction service
- Future projects that consume or produce Acessilia canonical structures

## Copyright and License

Copyright (c) 2026 Jhonata Fernandes Cordeiro
Copyright (c) 2026 Marcelo Inuzuka and Acessilia Dataset contributors

This project is licensed under the MIT License.
See the [LICENSE](LICENSE) file for the full license text.
