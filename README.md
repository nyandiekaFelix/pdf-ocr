# pdf-ocr

![Version 0.0.1](https://img.shields.io/badge/Version-0.0.1-blue) [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg) ](https://opensource.org/licenses/MIT)[![Built with Python](https://img.shields.io/badge/Built%20with-Python-green)](https://www.python.org/)

**pdf-ocr** is a Flask-based web service designed to perform Optical Character Recognition (OCR) on PDF files using machine vision and AI models. Built on PyTorch and Transformers and optimized with NVIDIA CUDA, this API provides two endpoints, one for OCR processing, and one for listing available models. This API is wrapped in a Docker container.

### OCR Process Overview

When a user submits a file to the /ocr endpoint, the following steps are executed:

1.  **Receive the Request:**
    - The server accepts a POST request containing the PDF file and optional parameters for OCR settings.
2.  **Extract and Open the PDF:**
    - The PDF file is extracted from the form data and opened to access its content.
3.  **Configure OCR Parameters:**
    - Parameters for the OCR process, such as the model and image processing settings, are set with defaults applied where not specified.
      - Optional parameters are read from the form data, such as `model`, `threshold_value`, `kernel_width`, `kernel_height`, and `min_area`.
      - Defaults are used for any parameters not provided.
4.  **Process Each Page:**
    - Each page of the PDF is processed sequentially. The steps include:
      - Rendering the page as an image.
      - Converting the image to grayscale and applying binary thresholding.
      - Performing morphological operations to enhance image clarity.
      - Extracting lines using contour detection and filtering by area.
5.  **Extract Text:**
    - Text is extracted from each line of the image using the TrOCR model. The text from all lines is compiled into a single output.
6.  **Return the Response:**
    - The extracted text is sent back in a JSON response.
7.  **Handle Errors:**
    - Errors during processing are caught and returned as a detailed error message.

## Dependencies

- **Python**: The script runs in a Python3 environment.
- **Flask**: Serves as the backbone of the web application, facilitating the creation of endpoints and handling HTTP requests.
- **google-protobuf**: Utilized for data serialization and deserialization, important for model loading and configuration.
- **gunicorn**: An extension that provides a Python WSGI HTTP Server for UNIX.
- **numpy**: Supports high-performance operations on large multi-dimensional arrays and matrices, used extensively in image manipulation.
- **OpenCV (opencv-python-headless)**: Used to segment larger bodies of text into individual lines.
- **Pillow (PIL)**: Helps with image processing tasks through the Python Imaging Library (Fork).
- **PyMuPDF (fitz)**: Utilized for PDF parsing with Python bindings for the MuPDF library.
- **sentencepiece**: Helps with unsupervised text tokenization and detokenization.
- **torch**: Utilized for machine learning tasks in computer vision and natural language processing.
- **transformers**: State-of-the-art Natural Language Processing for TensorFlow 2.0 and PyTorch.

### Installation

To install llm-pdf-ocr-api, follow these steps:

Begin by cloning the repository containing the llm-newsletter-generator to your local machine.

```bash
git clone https://github.com/nyandiekaFelix/pdf-ocr/
```

Navigate to the project directory:

```bash
cd llm-pdf-ocr-api
```

Install the required dependencies using pip:

```bash
pip install -r src/requirements.txt
```

## Endpoints

### OCR

**Endpoint:** `/ocr` **Method:** POST

Process a PDF file and return the extracted text.

- `file`: PDF file
- `model` (optional): Specifies the OCR model to be used for text extraction. Defaults to microsoft/trocr-base-printed if not provided.
- `threshold_value` (optional): Determines the threshold value for binary thresholding of images. The default value is 150.
- `kernel_width` (optional): Defines the width of the kernel used in morphological operations to clean up the image. It defaults to 20.
- `kernel_height` (optional): Specifies the height of the kernel used in morphological operations. The default is 1.
- `min_area` (optional): Sets the minimum area of contours that are considered as valid lines of text. The default minimum area is 50.

**Endpoint:** `/models` **Method:** GET

Show all AI models available.

## Error Handling

The API handles errors gracefully and returns appropriate error responses:

- **400 Bad Request**: Invalid request parameters.
- **500 Internal Server Error**: Unexpected server error.
