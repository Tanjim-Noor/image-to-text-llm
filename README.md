# PDF to Markdown with Text and Image OCR using Gemini API.

This Python project extracts text from PDF files, including performing OCR on embedded images using Google's Gemini API. It aims to preserve the document structure and outputs the combined text and OCR results into a Markdown file.

## Description

The script processes a specified range of pages within a PDF document. For each page, it:
1.  Extracts standard text content using PyMuPDF.
2.  Identifies and extracts images.
3.  Converts each image to base64.
4.  Sends the image to the Gemini Pro Vision API to perform OCR, with a prompt engineered to preserve text hierarchy and formatting.
5.  Combines the standard text and the OCR results from images into a structured Markdown output file, separating content by page.

This tool is particularly useful for digitizing PDFs where text might be embedded within images or for documents where preserving the original layout and formatting (like headings, lists, and colored text) is important.

## Features

* **Hybrid Text Extraction**: Combines standard text extraction from PDFs with advanced OCR for text within images.
* **Layout Preservation Focus**: The OCR prompt is designed to instruct the Gemini model to maintain text hierarchy (titles, subheadings, body text), lists, and special formatting.
* **Page Range Processing**: Allows users to specify a start and end page for processing.
* **Image Handling**: Converts images to JPEG format and handles potential transparency issues before sending to the OCR API.
* **Markdown Output**: Generates a clean, well-structured Markdown file with clear separation of content from each page and distinct sections for standard text and image OCR results.
* **Error Handling**: Includes basic error handling for file operations, API calls, and image processing.
* **Configuration**: Easy-to-configure variables for PDF path, output path, page range, and API key.
* **Rate Limiting Consideration**: Includes a small delay between API calls to help avoid rate-limiting issues.

## Requirements

* Python 3.7+
* PyMuPDF (`fitz`)
* Pillow (`PIL`)
* python-dotenv
* LangChain (`langchain_core`, `langchain_google_genai`)
* Google API Key with Gemini Pro Vision access.

## Setup and Installation

1.  **Clone the repository (if applicable) or download the script.**

2.  **Create a virtual environment (recommended):**
    ```bash
    python -m venv venv
    source venv/bin/activate  # On Windows use `venv\Scripts\activate`
    ```

3.  **Install the required packages:**
    ```bash
    pip install PyMuPDF Pillow python-dotenv langchain-core langchain-google-genai
    ```

4.  **Set up your Google API Key:**
    * Obtain a Google API Key from [Google AI Studio](https://aistudio.google.com/app/apikey).
    * Create a `.env` file in the root directory of the project.
    * Add your API key to the `.env` file:
        ```env
        GOOGLE_API_KEY="YOUR_GOOGLE_API_KEY_HERE"
        ```

## Usage

1.  **Configure the script:**
    Open the Python script (`your_script_name.py`) and modify the following variables in the "Configuration" section:
    * `pdf_file`: Path to your input PDF file (e.g., `"documents/my_document.pdf"`).
    * `output_file`: Desired path for the output Markdown file (e.g., `"output/ocr_results.md"`).
    * `start_page`: The page number to start processing from (1-based index).
    * `end_page`: The page number to end processing at (inclusive). Set to `0` or leave empty to process until the last page.
    * **AI Model**: The script currently uses `gemini-2.0-flash`. You can change to other compatible Gemini models (e.g., `gemini-1.5-flash`, `gemini-1.5-pro`, `gemini-1.0-pro`, etc.) by modifying the `model` parameter in the `get_ocr_text_from_image` function. For example:
    ```python
    # Inside get_ocr_text_from_image function:
    llm = ChatGoogleGenerativeAI(model="YOUR_DESIRED_GEMINI_MODEL", google_api_key=api_key)
    ```
    Ensure the chosen model is available and supports vision capabilities if you intend to use it for image OCR.

2.  **Run the script:**
    ```bash
    python your_script_name.py
    ```

3.  **Check the output:**
    The script will print progress to the console, including page numbers being processed and images found. Once completed, the extracted text and OCR results will be saved to the specified `output_file`.

## Script Overview

### Configuration
The script starts with a configuration section where you define:
* `GOOGLE_API_KEY`: Loaded from environment variables.
* `pdf_file`: Path to the input PDF.
* `output_file`: Path for the generated Markdown file.
* `start_page`, `end_page`: Page range for processing.

### Helper Functions

* **`image_to_base64(image: Image.Image, format="JPEG") -> str`**:
    * Converts a PIL Image object to a base64 encoded string.
    * Handles RGBA and Palette image modes by converting them to RGB (with a white background for RGBA) before saving as JPEG.

* **`get_ocr_text_from_image(image_base64: str, api_key: str) -> str`**:
    * Initializes the `ChatGoogleGenerativeAI` model (`gemini-2.0-flash`).
    * Constructs a `HumanMessage` containing a detailed prompt and the base64 image URL.
    * The prompt specifically asks the Gemini model to perform OCR while preserving document structure (hierarchy, lists, special formatting, colored text) and to output in Markdown.
    * Includes a `time.sleep(1.5)` to potentially mitigate API rate limiting.
    * Returns the OCR text from the model or an error message if the process fails.

* **`process_pdf_page_with_ocr(page: fitz.Page, llm_api_key: str) -> tuple[str, list[dict]]`**:
    * Extracts standard text from the page using `page.get_text("text")`.
    * Retrieves all images from the page using `page.get_images(full=True)`.
    * For each image:
        * Extracts image bytes.
        * Opens the image using PIL.
        * Converts the image to base64 using `image_to_base64`.
        * Calls `get_ocr_text_from_image` to get the OCR text.
        * Stores the OCR result along with its original index and XRef.
    * Returns the extracted standard page text and a list of image OCR results.

### Main Workflow

* **`pdf_to_combined_text(pdf_path: str, output_path: str, start_pg: int, end_pg: int, api_key: str)`**:
    * Checks if the PDF file exists and the API key is provided.
    * Opens the PDF using `fitz.open()`.
    * Validates and adjusts the page range (0-based for `fitz`, 1-based for user input).
    * Iterates through the specified page range:
        * Loads the current page.
        * Calls `process_pdf_page_with_ocr` to get standard text and image OCR results.
        * Formats the output for the current page, including a page header, standard text, and OCR results for each image (marked with placeholders and XRefs, and formatted in Markdown code blocks).
        * Appends the formatted content to `final_output`.
    * Closes the PDF document.
    * Writes the `final_output` to the specified Markdown file.

### Execution
The `if __name__ == "__main__":` block ensures that `pdf_to_combined_text` is called when the script is executed directly, provided the `GOOGLE_API_KEY` is available.

## Error Handling

* Checks for the existence of the PDF file.
* Checks for the presence of the `GOOGLE_API_KEY`.
* Handles errors during LLM initialization and API calls for OCR.
* Handles errors during standard text extraction and image processing within a page.
* Includes `try-except` blocks for file writing operations.
* Error messages are printed to the console and, where appropriate, included in the output Markdown to indicate failures for specific parts (e.g., `[Image OCR Failed: ...]`).

## Potential Future Improvements

* **Advanced Layout Reconstruction**: While the current prompt aims to preserve hierarchy, more sophisticated techniques could be explored to reconstruct complex layouts (e.g., multi-column text, tables) more accurately.
* **Placeholder Replacement**: Instead of just listing OCR text, integrate it more seamlessly into the standard text flow if image positions can be reliably determined.
* **Batch Processing**: Add functionality to process multiple PDF files in a directory.
* **Retry Logic**: Implement more robust retry mechanisms for API calls (e.g., exponential backoff for rate limit errors).
* **Confidence Scores**: If the OCR API provides confidence scores, these could be included in the output.
* **Selective OCR**: Option to only perform OCR on images larger than a certain size or in specific regions.
* **Asynchronous Processing**: For very large documents or many images, use `asyncio` to speed up API calls.
* **Alternative OCR Engines**: Allow for easy integration of other OCR engines as a fallback or alternative.

## License

This project is licensed under the MIT License.