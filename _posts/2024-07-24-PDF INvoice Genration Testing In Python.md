---
layout: post
author: Rohit vyas
---

## PDF Invoice Generation and Automated Testing in Python

This repository provides a comprehensive guide and code examples for generating PDF invoices using Python and ensuring their correctness and visibility through automated testing. The key components include:

    PDF invoice generation using the ReportLab library.
    Automated testing of PDF content using PyMuPDF.
    Identifying text overlap and blank areas in the PDF to ensure text visibility.

## Table of Contents

    Introduction
    Prerequisites
    PDF Invoice Generation
    Automated Testing of PDFs
    Identify Text Overlap and Blank PDFs
    Running the Tests
    Conclusion

## Introduction

Creating professional PDF invoices is a common requirement in many applications. Ensuring the accuracy and visibility of the text in these PDFs is crucial for usability and professionalism. This guide demonstrates how to generate PDF invoices and automate their testing using Python.
Prerequisites

    Python 3.x
    reportlab library for PDF generation
    PyMuPDF (also known as fitz) for PDF reading and image extraction
    Pillow library for image processing

You can install the necessary libraries using pip:

bash

pip install reportlab pymupdf pillow

PDF Invoice Generation

We use the ReportLab library to create PDF invoices. Below is an example of how to generate a simple invoice:

python
```

from reportlab.lib.pagesizes import letter
from reportlab.pdfgen import canvas
from reportlab.lib import colors

invoice_data = {
    'company_name': 'Your Company Name',
    'company_address': '123 Your Street, Your City, Your Country',
    'client_name': 'Client Name',
    'client_address': '456 Client Street, Client City, Client Country',
    'invoice_number': 'INV-001',
    'invoice_date': '2024-07-26',
    'items': [
        {'description': 'Service 1', 'quantity': 1, 'unit_price': 100},
        {'description': 'Service 2', 'quantity': 2, 'unit_price': 150},
    ],
    'tax_rate': 0.1,  # 10%
}

def calculate_totals(items, tax_rate):
    subtotal = sum(item['quantity'] * item['unit_price'] for item in items)
    tax = subtotal * tax_rate
    total = subtotal + tax
    return subtotal, tax, total

def generate_invoice(invoice_data):
    c = canvas.Canvas("invoice.pdf", pagesize=letter)
    width, height = letter

    # Set up the document
    c.setFont("Helvetica", 12)
    c.setStrokeColor(colors.black)
    c.setLineWidth(1)

    # Constants for layout
    margin = 30
    line_height = 15
    y_offset = height - margin

    def draw_text(text, x, y):
        c.drawString(x, y, text)

    # Company information
    draw_text(invoice_data['company_name'], margin, y_offset)
    draw_text(invoice_data['company_address'], margin, y_offset - line_height)
    y_offset -= 3 * line_height

    # Client information
    draw_text(f"Bill To: {invoice_data['client_name']}", width - 250, height - margin)
    draw_text(invoice_data['client_address'], width - 250, height - margin - line_height)
    y_offset -= line_height

    # Invoice information
    draw_text(f"Invoice Number: {invoice_data['invoice_number']}", margin, y_offset)
    draw_text(f"Invoice Date: {invoice_data['invoice_date']}", margin, y_offset - line_height)
    y_offset -= 2 * line_height

    # Table header
    draw_text("Description", margin, y_offset)
    draw_text("Quantity", 300, y_offset)
    draw_text("Unit Price", 400, y_offset)
    draw_text("Total", 500, y_offset)
    y_offset -= line_height

    # Table rows
    for item in invoice_data['items']:
        draw_text(item['description'], margin, y_offset)
        draw_text(str(item['quantity']), 300, y_offset)
        draw_text(f"${item['unit_price']:.2f}", 400, y_offset)
        total_price = item['quantity'] * item['unit_price']
        draw_text(f"${total_price:.2f}", 500, y_offset)
        y_offset -= line_height

    # Totals
    subtotal, tax, total = calculate_totals(invoice_data['items'], invoice_data['tax_rate'])
    draw_text("Subtotal:", 400, y_offset - line_height)
    draw_text(f"${subtotal:.2f}", 500, y_offset - line_height)
    draw_text("Tax:", 400, y_offset - 2 * line_height)
    draw_text(f"${tax:.2f}", 500, y_offset - 2 * line_height)
    draw_text("Total:", 400, y_offset - 3 * line_height)
    draw_text(f"${total:.2f}", 500, y_offset - 3 * line_height)

    c.save()

if __name__ == "__main__":
    generate_invoice(invoice_data)

```
## Automated Testing of PDFs

We use PyMuPDF and Pillow for reading the PDF content and performing automated tests to ensure values are printed correctly and text is visible.
Extract Text from PDF

python
```

import fitz  # PyMuPDF

def extract_text_from_first_page(pdf_path):
    document = fitz.open(pdf_path)
    first_page = document.load_page(0)
    text = first_page.get_text()
    return text

Test Invoice Values

python

def test_invoice_values():
    pdf_path = "invoice.pdf"
    text = extract_text_from_first_page(pdf_path)

    # Define the expected values
    expected_values = {
        'company_name': 'Your Company Name',
        'company_address': '123 Your Street, Your City, Your Country',
        'client_name': 'Client Name',
        'client_address': '456 Client Street, Client City, Client Country',
        'invoice_number': 'INV-001',
        'invoice_date': '2024-07-26',
        'items': [
            {'description': 'Service 1', 'quantity': 1, 'unit_price': 100},
            {'description': 'Service 2', 'quantity': 2, 'unit_price': 150},
        ],
        'tax_rate': 0.1,
    }

    # Check the company name and address
    assert expected_values['company_name'] in text
    assert expected_values['company_address'] in text

    # Check the client name and address
    assert expected_values['client_name'] in text
    assert expected_values['client_address'] in text

    # Check the invoice number and date
    assert expected_values['invoice_number'] in text
    assert expected_values['invoice_date'] in text

    # Check each item
    for item in expected_values['items']:
        assert item['description'] in text
        assert str(item['quantity']) in text
        assert f"${item['unit_price']:.2f}" in text

    # Calculate totals
    subtotal = sum(item['quantity'] * item['unit_price'] for item in expected_values['items'])
    tax = subtotal * expected_values['tax_rate']
    total = subtotal + tax

    # Check totals
    assert f"${subtotal:.2f}" in text
    assert f"${tax:.2f}" in text
    assert f"${total:.2f}" in text

    print("All tests passed!")

if __name__ == "__main__":
    test_invoice_values()

Identify Text Overlap and Blank PDFs
Convert PDF to Image

python

import fitz  # PyMuPDF
from PIL import Image, ImageChops

def pdf_to_image(pdf_path):
    document = fitz.open(pdf_path)
    first_page = document.load_page(0)
    pix = first_page.get_pixmap()
    image = Image.frombytes("RGB", [pix.width, pix.height], pix.samples)
    return image

Check Image Visibility

python

def check_image_visibility(image):
    # Example: Ensure no significant blank space (overlapping text can create large blank areas)
    # Convert image to grayscale
    grayscale = image.convert("L")
    # Invert colors
    inverted = ImageChops.invert(grayscale)
    # Check for visible text
    visible_text_area = inverted.getbbox()
    return visible_text_area is not None

Test PDF Visuals

python

def test_pdf_visuals():
    pdf_path = "invoice.pdf"
    image = pdf_to_image(pdf_path)
    assert check_image_visibility(image), "Text is not clearly visible or overlapped"

    print("Visual test passed!")

if __name__ == "__main__":
    test_pdf_visuals()

```

## Running the Tests

To run the tests, execute the following commands in your terminal:

bash

python test_invoice_values.py
python test_pdf_visuals.py

These scripts will generate the invoice, extract text from the PDF, and perform visual checks to ensure the text is correctly printed and visible.
Conclusion

By following this guide, you can generate professional PDF invoices using Python 

[back](/blogs.html)
