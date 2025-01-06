import os
import json
from bs4 import BeautifulSoup
from PyPDF2 import PdfReader
from pdf2image import convert_from_path
import pytesseract

# Utility Functions
def extract_text_from_pdf(pdf_path):
    """Extract text from a PDF file."""
    try:
        reader = PdfReader(pdf_path)
        text = "\n".join(page.extract_text() for page in reader.pages if page.extract_text())
        if not text.strip():
            raise ValueError("Empty text from PDF, falling back to OCR.")
        return text
    except Exception as e:
        print(f"PDF extraction failed for {pdf_path}: {e}. Using OCR.")
        return ocr_pdf(pdf_path)

def ocr_pdf(pdf_path):
    """Perform OCR on a PDF file."""
    images = convert_from_path(pdf_path)
    return "\n".join(pytesseract.image_to_string(image) for image in images)

def extract_text_from_html(html_path):
    """Extract text from an HTML file."""
    with open(html_path, 'r', encoding='utf-8') as file:
        soup = BeautifulSoup(file, 'html.parser')
        return soup.get_text(separator="\n")

def map_to_fields(text):
    """Map extracted text to structured fields."""
    fields = {
        "bid_number": None,
        "title": None,
        "due_date": None,
        "bid_submission_type": None,
        "term_of_bid": None,
        "prebid_meeting": None,
        "installation": None,
        "bid_bond_requirement": None,
        "delivery_date": None,
        "payment_terms": None,
        "additional_documentation": None,
        "mfg_registration": None,
        "contract_coop_use": None,
        "model_no": None,
        "part_no": None,
        "product": None,
        "contact_info": None,
        "company_name": None,
        "bid_summary": None,
        "product_specification": None,
    }

    # Example Extraction Logic
    for line in text.splitlines():
        if "bid number" in line.lower():
            fields["bid_number"] = line.split(":")[-1].strip()
        if "title" in line.lower():
            fields["title"] = line.split(":")[-1].strip()
        if "due date" in line.lower():
            fields["due_date"] = line.split(":")[-1].strip()
        if "submission type" in line.lower():
            fields["bid_submission_type"] = line.split(":")[-1].strip()
        # Add similar logic for other fields

    return fields

# Process Files
def process_files(input_files):
    results = {}
    for file_path in input_files:
        _, ext = os.path.splitext(file_path)
        try:
            if ext.lower() == ".pdf":
                text = extract_text_from_pdf(file_path)
            elif ext.lower() in [".html", ".htm"]:
                text = extract_text_from_html(file_path)
            else:
                raise ValueError("Unsupported file format.")
            results[os.path.basename(file_path)] = map_to_fields(text)
        except Exception as e:
            print(f"Error processing {file_path}: {e}")
    return results

# Define your file paths
input_files = [
    r"C:\Users\bhava\Downloads\UVCE_BTech_Emplay_Assignment\input files\Bid1\Addendum 1.pdf",
    r"C:\Users\bhava\Downloads\UVCE_BTech_Emplay_Assignment\input files\Bid1\Addendum 2.pdf",
    r"C:\Users\bhava\Downloads\UVCE_BTech_Emplay_Assignment\input files\Bid1\Bid Information.html",
]

# Process and Save Results
output = process_files(input_files)
with open("output.json", "w", encoding="utf-8") as f:
    json.dump(output, f, indent=4)
print("Processing complete. Results saved to output.json.")
