---
name: pdf
description: A skill for processing PDF files
---

# PDF Processing Skill

## Purpose
This skill helps you work with PDF files.

## Common Tasks
1. Extract text from PDF
2. Merge multiple PDFs
3. Split PDF into pages
4. Add watermarks
5. Convert to other formats

## Tools Available
- PyPDF2: For reading and writing PDFs
- pdfplumber: For extracting text and tables
- reportlab: For creating PDFs
- pdf2image: For converting PDFs to images

## Example Code
```python
import PyPDF2

# Read PDF
with open('document.pdf', 'rb') as f:
    reader = PyPDF2.PdfReader(f)
    text = ''
    for page in reader.pages:
        text += page.extract_text()

# Write PDF
writer = PyPDF2.PdfWriter()
writer.add_page(page)
with open('output.pdf', 'wb') as f:
    writer.write(f)
```
