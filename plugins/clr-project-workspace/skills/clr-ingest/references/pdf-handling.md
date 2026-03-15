# PDF Handling

## Reading PDFs with the Read Tool

The Read tool can read PDF files directly using the `pages` parameter.

- For small PDFs (10 pages or fewer), read without specifying pages
- For large PDFs, read in chunks: `pages: "1-5"`, then `pages: "6-10"`, etc.
- Maximum 20 pages per Read call
- The Read tool returns the visual content of PDF pages — useful for both text-based and image-based PDFs

## Text Extraction: Two-Step Approach

### Step 1: Try Standard Text Extraction First

Use pypdf or pdfplumber to extract text programmatically:

```python
# Using pypdf
from pypdf import PdfReader

reader = PdfReader("document.pdf")
text = ""
for page in reader.pages:
    text += page.extract_text() or ""
```

```python
# Using pdfplumber (better for tables)
import pdfplumber

with pdfplumber.open("document.pdf") as pdf:
    text = ""
    for page in pdf.pages:
        text += page.extract_text() or ""
```

If meaningful text comes back (not empty, not gibberish), use it. This is faster and more accurate for text-based PDFs.

### Step 2: Fall Back to OCR If Needed

If text extraction returns empty or near-empty results, the PDF is image-based. Use pdf2image + pytesseract:

```python
from pdf2image import convert_from_path
import pytesseract

images = convert_from_path("document.pdf", dpi=300)
text = ""
for image in images:
    text += pytesseract.image_to_string(image) + "\n"
```

OCR is slower and less accurate than direct text extraction. Only use it when Step 1 fails.

## Compression for Oversized PDFs

Some PDFs are oversized because they were created by exporting photos or screenshots. To compress:

```python
from pdf2image import convert_from_path
from PIL import Image

# Rasterize at 200 DPI (good balance of quality vs size)
images = convert_from_path("oversized.pdf", dpi=200)

# Re-save with compression
for i, image in enumerate(images):
    image.save(
        f"page_{i}.jpg",
        "JPEG",
        quality=60,
        optimize=True
    )
```

To recombine into a PDF:

```python
images = [Image.open(f"page_{i}.jpg") for i in range(len(images))]
images[0].save(
    "compressed.pdf",
    "PDF",
    save_all=True,
    append_images=images[1:],
    quality=60,
    optimize=True
)
```

## Dependencies

| Package | Purpose | Install |
|---------|---------|---------|
| pypdf | Text extraction (text-based PDFs) | `pip install pypdf` |
| pdfplumber | Text extraction (better for tables) | `pip install pdfplumber` |
| pdf2image | Convert PDF pages to images (for OCR and compression) | `pip install pdf2image` |
| pytesseract | OCR text extraction from images | `pip install pytesseract` |
| Pillow | Image processing and compression | `pip install Pillow` |
| Tesseract | OCR engine (system dependency) | `brew install tesseract` (Mac) |

Note: pdf2image requires poppler. Install with `brew install poppler` on Mac.

## Never Use Ghostscript

Do not use Ghostscript for any PDF operations. It's an unnecessary system dependency. pdf2image + Pillow cover all compression needs, and pypdf/pdfplumber + pytesseract cover all extraction needs.

## Saving PDF Content to the Workspace

After extracting text from a PDF:

1. Create a clean markdown reference doc in notes/ with the extracted content
2. Note the original filename and that it was extracted from PDF
3. If the original PDF has lasting value (contracts, official documents, formal correspondence), save it in notes/ alongside the markdown extraction
4. Don't keep both if the markdown version captures everything — the extracted version is easier to search and reference
