# Jina OCR with Elastic Inference Service (EIS)

This example demonstrates how to extract text from an image using **Jina OCR** through the **Elastic Inference Service (EIS)** and the Elasticsearch Inference API.

The included Jupyter notebook sends an image to a chat completion inference endpoint, receives the model's response as a stream, and combines the streamed chunks into the extracted text.

## How it works

1. Load a local JPEG image.
2. Encode the image as Base64.
3. Send the image and an OCR instruction to the Elasticsearch inference endpoint.
4. Read the streamed response and concatenate the returned text fragments.
5. Display the original image and print the extracted text.

```text
Local JPEG image
      |
      v
Python notebook (Base64 encoding)
      |
      v
Elasticsearch Inference API
      |
      v
Jina OCR via Elastic Inference Service
      |
      v
Streaming response -> Extracted text
```

## Prerequisites

- Python with Jupyter Notebook or JupyterLab.
- Access to an Elasticsearch deployment with a configured Jina OCR chat completion inference endpoint.
- An Elasticsearch API key authorized to call that endpoint.
- A local JPEG image to process.

You can find the inference endpoint ID using the following request in Kibana Dev Tools:

```http
GET _inference
```

The notebook uses `.jina-ocr-v1-chat_completion` as its default inference endpoint ID. Verify that this endpoint exists in your environment.

## Getting started

1. Download or clone this example and open `jina_ocr_sample (2).ipynb` in Jupyter.
2. Place your JPEG image in the same directory as the notebook, or specify its full path.
3. Run **Step 1** to install `requests`.
4. In **Step 2**, configure your Elasticsearch connection and image path:

```python
ES_URL       = "https://your-elasticsearch-endpoint"
ES_API_KEY   = "YOUR_API_KEY_HERE"
INFERENCE_ID = ".jina-ocr-v1-chat_completion"
IMAGE_PATH   = "open-jar.jpeg"
```

5. Run **Step 3** to submit the image for OCR and collect the streaming response.
6. Run **Step 4** to display the image and extracted text.

> **Note:** The notebook refers to `open-jar.jpeg`, but the image itself must be present locally. If it is not included with your copy, provide another JPEG image and update `IMAGE_PATH`.

## API request

The notebook calls:

```http
POST /_inference/chat_completion/{INFERENCE_ID}/_stream
Authorization: ApiKey <YOUR_API_KEY>
Content-Type: application/json
```

The request contains a user message with two content parts: a text instruction asking the model to extract text while preserving titles, steps, and tables as raw text, and an `image_url` containing the Base64-encoded JPEG as a data URL.

The response is streamed. The notebook reads each `data:` event, extracts `choices[].delta.content`, and joins those fragments into one string.

## Expected output

The final cell displays the source image and prints the OCR result below it. The actual extracted text depends on the image and the model response.

## Limitations and notes

- The example is configured for **JPEG** images (`data:image/jpeg`). For other formats, adjust the media type in the request accordingly.
- The notebook is a minimal demonstration, not a production-ready OCR pipeline. It does not include retries, request timeouts, or advanced streaming error handling.
- OCR quality and preservation of document structure depend on the input image and the model output.
- Keep API keys private. Avoid committing notebooks containing real credentials to version control.

## Files

| File | Description |
| --- | --- |
| `jina_ocr_sample (2).ipynb` | Jupyter notebook implementing the OCR example. |
| `open-jar.jpeg` | Example input image referenced by the notebook; provide this file locally if it is not included. |

## Technologies

- **Elasticsearch** — Inference API
- **Elastic Inference Service (EIS)** — Model inference service
- **Jina OCR** — OCR model
- **Python**, **requests**, and **Jupyter Notebook** — Example implementation
