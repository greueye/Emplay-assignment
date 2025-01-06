# Bid Information Extractor

This project extracts structured bid information from PDF or HTML documents, mapping it to predefined fields like "Bid Number," "Title," "Due Date," etc.

## Features
- Extract text from PDF or HTML files using OCR and parsing.
- Map text to structured fields using predefined logic.
- Output structured data as JSON.

## Requirements
Make sure to have the following installed:
- Python 3.7 or higher
- Required Python libraries:
  - `PyPDF2`
  - `pytesseract`
  - `pdf2image`
  - `beautifulsoup4`

Install dependencies using:
```bash
pip install PyPDF2 pytesseract pdf2image beautifulsoup4
