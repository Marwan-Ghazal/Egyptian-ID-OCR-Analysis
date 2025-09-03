# Arabic OCR Pipeline for Egyptian National ID

A comprehensive OCR (Optical Character Recognition) pipeline specifically designed for processing Egyptian National ID cards. This project combines multiple OCR engines and implements advanced preprocessing techniques to achieve high accuracy in Arabic text recognition.

## Features

- **Multi-Engine OCR Support**: Integrates Tesseract, EasyOCR, and PaddleOCR for optimal text recognition
- **Intelligent ID Detection**: Automatic detection and extraction of ID cards from images using contour analysis
- **Advanced Preprocessing**: Optimized image preprocessing pipeline for enhanced OCR accuracy
- **ROI Strategy**: Smart region-of-interest detection for ID number, address, and name fields
- **Semantic Validation**: Built-in validation for Egyptian National ID format and authenticity
- **FastAPI Integration**: RESTful API for easy integration into production systems

## Project Structure

```
Arabic-OCR-comparing/
├── ID_OCR_Pipeline.ipynb    # Main OCR pipeline notebook
├── README.md               # This file
├── sample_images/          # Test images
│   ├── marwanfront.jpg
│   ├── omarfront.jpg
│   └── other test images
└── requirements.txt        # Python dependencies
```

## Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd Arabic-OCR-comparing
```

2. Install required dependencies:
```bash
pip install -r requirements.txt
```

3. Install additional OCR engines:
```bash
# For Tesseract (Windows)
# Download and install from: https://github.com/UB-Mannheim/tesseract/wiki

# For Arabic language support
pip install pytesseract
pip install easyocr
pip install paddleocr
```

## Usage

### Jupyter Notebook

Open and run the `ID_OCR_Pipeline.ipynb` notebook:

```bash
jupyter notebook ID_OCR_Pipeline.ipynb
```

### Python Script Usage

```python
from paddleocr import PaddleOCR
import cv2

# Initialize OCR engine
ocr = PaddleOCR(lang='ar', use_angle_cls=True)

# Process an image
image_path = "path/to/your/id_image.jpg"
result = process_id_image(image_path)
print(result)
```

### FastAPI Server

The project includes a FastAPI server for production deployment:

```bash
# Run the API server
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

API endpoints:
- `POST /ocr/process` - Process ID image and extract text
- `GET /health` - Health check endpoint

## Pipeline Overview

### 1. Image Preprocessing
- **Grayscale Conversion**: Convert to single channel for processing
- **Gaussian Blur**: 3×3 kernel with σ≈3 for noise reduction
- **Adaptive Thresholding**: Gaussian method with block size 7, C=2
- **Median Blur**: 3×3 kernel for salt-and-pepper noise removal
- **Optional Erosion**: 2×2 kernel for fine-tuning

### 2. ID Detection
- Contour detection using edge detection algorithms
- Rectangle candidate collection via `minAreaRect`
- Largest rectangular region identification
- Perspective correction and cropping

### 3. ROI Extraction
- **Name Field**: Upper portion of the ID
- **Address Field**: Middle section with expanded margins
- **ID Number**: Lower section with precise cropping
- **Cushioned Cropping**: Widened margins for better OCR accuracy

### 4. OCR Processing
- Multi-engine approach using Tesseract, EasyOCR, and PaddleOCR
- Arabic RTL (Right-to-Left) text handling
- Confidence scoring and result validation

### 5. Semantic Validation
- **14-digit ID Format**: Validates Egyptian National ID structure
- **Date of Birth Parsing**: Extracts and validates DOB from ID number
- **Governorate Code**: Validates location code within ID
- **Fake ID Detection**: Implements authenticity checks

## Technical Implementation

### OCR Engines Comparison

| Engine | Arabic Support | Accuracy | Speed | Memory Usage |
|--------|---------------|----------|-------|--------------|
| PaddleOCR | Excellent | High | Fast | Medium |
| EasyOCR | Good | Medium | Medium | High |
| Tesseract | Good | Medium | Fast | Low |

### Preprocessing Parameters

```python
# Optimal parameters found through experimentation
GAUSSIAN_BLUR_KERNEL = (3, 3)
GAUSSIAN_SIGMA = 3
ADAPTIVE_THRESH_BLOCK_SIZE = 7
ADAPTIVE_THRESH_C = 2
MEDIAN_BLUR_KERNEL = 3
EROSION_KERNEL = (2, 2)
```

## Validation Features

### National ID Validation
- Format: 14 digits (2YY MM DD GG NNN G)
- Century validation (2/3 for 1900s/2000s)
- Month validation (01-12)
- Day validation (01-31)
- Governorate code validation

### Authenticity Checks
- Checksum validation
- Format consistency
- Logical date validation
- Governorate code verification

## API Documentation

### Process ID Endpoint

```http
POST /ocr/process
Content-Type: multipart/form-data

Parameters:
- file: Image file (JPEG, PNG)
- engine: OCR engine ("paddle", "easy", "tesseract")
```

Response:
```json
{
  "status": "success",
  "data": {
    "name": "extracted name",
    "address": "extracted address",
    "id_number": "12345678901234",
    "confidence": 0.95,
    "validation": {
      "is_valid": true,
      "birth_date": "1990-01-01",
      "governorate": "Cairo"
    }
  }
}
```

## Performance Optimization

- **Parallel Processing**: Multi-threaded OCR processing
- **Memory Management**: Efficient image handling for large files
- **Caching**: Result caching for repeated requests
- **Batch Processing**: Support for multiple images

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/new-feature`)
3. Commit your changes (`git commit -am 'Add new feature'`)
4. Push to the branch (`git push origin feature/new-feature`)
5. Create a Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- PaddleOCR team for excellent Arabic OCR support
- OpenCV community for computer vision tools
- Tesseract OCR project for open-source OCR engine
- EasyOCR for user-friendly OCR implementation

## Contact

For questions or support, please open an issue in the repository.

---

**Note**: This pipeline is specifically optimized for Egyptian National ID cards. For other document types or regions, parameters may need adjustment.
