# Acessilia Dataset

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Test documents and expected extraction outputs for the [Acessilia](https://github.com/A11yDevs/acessilia) ecosystem.

## Overview

**Acessilia Dataset** provides a shared collection of test documents and their corresponding expected structural manifests for projects in the Acessilia ecosystem, including:

- [acessilia-structure-extractor](https://github.com/A11yDevs/acessilia-structure-extractor) — document structure extraction service
- [acessilia](https://github.com/A11yDevs/acessilia) — main accessibility processing platform

By maintaining documents and expected outputs in a single repository, all consuming projects can validate their extraction pipelines against the same reference data, ensuring consistent behavior across the ecosystem.

## Repository Structure

```text
acessilia-dataset/
├── README.md
├── LICENSE
├── documents/                          # Source documents for testing
│   ├── simple/                         # Simple, synthetic documents
│   │   ├── one-page.pdf                #   1 page, title + paragraph
│   │   └── one-page.png                #   Same content as image
│   ├── tutorials/                      # Real-world tutorial PDFs
│   │   └── java-oo-3pgs.pdf            #   3-page Java OO tutorial
│   ├── with-tables/                    # Documents containing tables
│   │   └── budget-table.pdf
│   └── with-images/                    # Documents with figures
│       └── scanned-doc.pdf
├── expected/                           # Expected extraction outputs
│   └── processing-manifest/            # ProcessingManifest JSON snapshots
│       ├── one-page.json
│       ├── java-oo-3pgs.json
│       ├── budget-table.json
│       └── scanned-doc.json
└── scripts/                            # Automation scripts
    ├── generate-all.sh                 # Regenerate all expected outputs
    └── README.md
```

## Document Categories

| Category | Description | Source |
|---|---|---|
| `simple/` | Synthetic PDFs and images with controlled content | Generated via PyMuPDF |
| `tutorials/` | Real-world educational PDFs | Acessilia project fixtures |
| `with-tables/` | Documents containing structured tables | Synthetic + real-world |
| `with-images/` | Documents with figures, photos, scanned pages | Synthetic + real-world |

## Expected Outputs

Each document in `documents/` has a corresponding JSON file in `expected/processing-manifest/` containing the **ProcessingManifest** — the canonical structural representation defined by the Acessilia ecosystem.

The expected outputs are generated using a **reference extractor** (either the original Acessilia or the structure-extractor) and committed to this repository so that:

- **Consuming projects** can validate their extraction against known-good snapshots
- **CI/CD pipelines** can detect regressions when extraction logic changes
- **New documents** can be added with confidence by generating their expected output automatically

## Usage

### As a git submodule

```bash
# In your project
git submodule add https://github.com/A11yDevs/acessilia-dataset.git tests/dataset
```

Then reference documents and expected outputs relative to the submodule path:

```python
from pathlib import Path

DATASET_DIR = Path("tests/dataset")


def get_fixtures() -> list[Path]:
    return sorted(DATASET_DIR.glob("documents/**/*.pdf"))


def get_expected(fixture_path: Path) -> Path:
    relative = fixture_path.relative_to(DATASET_DIR / "documents")
    name = relative.with_suffix(".json")
    return DATASET_DIR / "expected" / "processing-manifest" / name
```

### As a Python package (optional)

```bash
pip install acessilia-dataset@git+https://github.com/A11yDevs/acessilia-dataset.git
```

```python
from acessilia_dataset import get_documents_dir, get_expected_dir

for doc in get_documents_dir().rglob("*.pdf"):
    print(doc)
```

## Regenerating Expected Outputs

When the extraction pipeline changes (e.g., Docling version update), expected outputs must be regenerated:

```bash
# Using the original Acessilia as reference
bash scripts/generate-all.sh \
  "python3 /path/to/acessilia/scripts/manifest.py"

# Or using the structure-extractor itself
bash scripts/generate-all.sh "acessilia-extract"
```

After regeneration, review the diff and commit the updated snapshots.

## Contributing

Contributions of new test documents are welcome. Please follow these guidelines:

1. Add the source document to the appropriate `documents/` subdirectory
2. Generate the expected output using the reference extractor
3. Ensure the document is small (prefer under 1 MB) and does not contain copyrighted material unless properly licensed
4. Submit a pull request

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
