# Test Notebook: test_ocr_gemini.ipynb

## Overview
This notebook provides a clean, well-documented implementation of PDF OCR using Google Gemini 2.5 Flash with proper response handling based on latest LangChain and Google API documentation.

## Key Improvements Over Original

### 1. **Proper Response Handling**
The original code had this issue:
```python
# WRONG - response.content can be a list
if '[no text found in image]' in response.content.lower():  # Error: 'list' object has no attribute 'lower'
```

**Fixed with `extract_text_from_response()` function:**
```python
def extract_text_from_response(response) -> str:
    """Handles both string and list responses from Gemini API"""
    content = response.content
    if isinstance(content, str):
        return content
    if isinstance(content, list):
        text_parts = []
        for item in content:
            if isinstance(item, str):
                text_parts.append(item)
            elif isinstance(item, dict) and 'text' in item:
                text_parts.append(item['text'])
        return ''.join(text_parts)
    return str(content)
```

### 2. **Documentation References**
- **LangChain Google**: Utilizes `ChatGoogleGenerativeAI` with `gemini-2.5-flash` model
- **Google GenAI SDK**: Follows multimodal input patterns for base64-encoded images
- **Response Format**: Properly handles both string and list response types

### 3. **Better Error Handling**
- Detailed error messages for debugging
- Optional `debug` parameter for verbose output
- Full traceback printing on errors

### 4. **Optimized Configuration**
- Model: `gemini-2.5-flash` (recommended by latest docs)
- Temperature: `0.3` (lower for consistent OCR)
- Rate limiting: `1.5s` delay between API calls

## Structure

### Cell 1: Imports & Configuration
Loads all required libraries and validates API key setup.

### Cell 2: Helper Functions
- `image_to_base64()` - Converts PIL images to base64
- `extract_text_from_response()` - Robust response handling

### Cell 3: OCR Function
- `get_ocr_text_from_image()` - Core OCR with proper response extraction
- Includes debug mode for troubleshooting

### Cell 4: PDF Processing
- `process_pdf_page_with_ocr()` - Extracts and processes all images on a page
- Handles image extraction failures gracefully

### Cell 5-7: Testing
- Configuration setup
- Single page test with detailed output
- Multiple page batch testing

## How to Use

### Step 1: Setup
```python
PDF_PATH = "CourseBook_Semester3_AlTafsir.pdf"  # Your PDF file
TEST_PAGE = 1  # Page to test
```

### Step 2: Run Cell 1-4
These set up all functions. No changes needed.

### Step 3: Run Cell 5
Validates PDF exists and shows page count.

### Step 4: Run Cell 6
Tests single page with debug output:
- Shows page image count
- Displays response types
- Prints extracted text (first 300 chars)

### Step 5: Run Cell 7
Batch test multiple pages:
```python
TEST_PAGES = [1, 2, 3, 4, 5]  # Test pages 1-5
```

## Expected Output

✓ Successful test:
```
============================================================
Testing OCR on Page 1
============================================================

[PAGE 1] Found 1 images
  Image 1: Sending to OCR...
  Image 1: OCR successful (1250 chars)

============================================================
Results: 1 text block(s) extracted
============================================================

--- Text Block 1 (First 300 chars) ---
# Chapter Title
Main content text here...
... [Total: 1250 characters]
```

✗ If error occurs:
- Check PDF path
- Verify API key in .env
- Review error message with traceback

## Next Steps

### When Test Passes ✓
1. Copy the working functions to your production script
2. Increase page range: `start_page = 1, end_page = 102`
3. Adjust rate limiting if needed (change `time.sleep(1.5)`)

### When Test Fails ✗
1. Check error message and traceback
2. Verify PDF file exists and is valid
3. Confirm GOOGLE_API_KEY in .env file
4. Run with `debug=True` for detailed output

## API Documentation

### LangChain Google
- **Model**: `gemini-2.5-flash`
- **Response**: Always has `.content` attribute (string or list)
- **Multimodal**: Supports base64 image URLs in `data:image/jpeg;base64,...` format

### Google Gemini API
- **Rate Limits**: Use delays between requests
- **Response Types**: String for text-only, list for multimodal outputs
- **Error Handling**: Always wrap API calls in try-except

## Troubleshooting

| Problem | Solution |
|---------|----------|
| `KeyError: GOOGLE_API_KEY` | Set GOOGLE_API_KEY in .env file |
| PDF not found | Check PDF_PATH variable, verify file exists |
| API rate limit | Increase `time.sleep()` value |
| Empty extraction | Check PDF contains scanned images, not pure text |
| `'list' object has no attribute` | Use provided `extract_text_from_response()` function |

## Files
- `test_ocr_gemini.ipynb` - This test notebook
- `image_to_text_llm-model_v2.ipynb` - Original notebook (needs updating)

---
**Last Updated**: January 21, 2026
**Based on**: LangChain Google latest docs, Google GenAI SDK v1.33.0
