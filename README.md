## Prototype Development for Image Captioning Using the BLIP Model and Gradio Framework

### AIM:
To design and deploy a prototype application for image captioning by utilizing the BLIP image-captioning model and integrating it with the Gradio UI framework for user interaction and evaluation.

### PROBLEM STATEMENT:
To build a simple web application using Hugging Face API and Gradio that generates text captions for uploaded images using an image-to-text model.

### DESIGN STEPS:

#### Step 1: 
Import required libraries such as requests, gradio, dotenv, PIL, and base64.
#### Step 2: 
Load the Hugging Face API key using .env for secure access.
#### Step 3: 
Create a function to send the base64 encoded image to the Hugging Face captioning API using a POST request.
#### Step 4: 
Write a function to convert the uploaded image into a base64 string.
#### Step 5: 
Build a captioner function that returns the generated caption text.
#### Step 6: 
Design a Gradio interface with an image upload box and caption output textbox.
#### Step 7: 
Launch the web app using demo.launch() to generate captions for uploaded images.

### PROGRAM:
```
import os
import io
import IPython.display
from PIL import Image
import base64 
from dotenv import load_dotenv, find_dotenv
_ = load_dotenv(find_dotenv()) # read local .env file
hf_api_key = os.environ['HF_API_KEY']

# Helper functions
import requests, json

#Image-to-text endpoint
def get_completion(inputs, parameters=None, ENDPOINT_URL=os.environ['HF_API_ITT_BASE']):
    headers = {
      "Authorization": f"Bearer {hf_api_key}",
      "Content-Type": "application/json"
    }
    data = { "inputs": inputs }
    if parameters is not None:
        data.update({"parameters": parameters})
    response = requests.request("POST",
                                ENDPOINT_URL,
                                headers=headers,
                                data=json.dumps(data))
    return json.loads(response.content.decode("utf-8"))

import gradio as gr 

def image_to_base64_str(pil_image):
    byte_arr = io.BytesIO()'
    pil_image.save(byte_arr, format='PNG')
    byte_arr = byte_arr.getvalue()
    return str(base64.b64encode(byte_arr).decode('utf-8'))

def captioner(image):
    base64_image = image_to_base64_str(image)
    result = get_completion(base64_image)
    return result[0]['generated_text']

gr.close_all()
demo = gr.Interface(fn=captioner,
                    inputs=[gr.Image(label="Upload image", type="pil")],
                    outputs=[gr.Textbox(label="Caption")],
                    title="Image Captioning with BLIP",
                    description="Caption any image using the BLIP model Done by Junaid Sardar",
                    allow_flagging="never",
                    examples=["christmas_dog.jpeg", "bird_flight.jpeg", "cow.jpeg"])

demo.launch(share=True, server_port=int(os.environ['PORT1']))
```
### OUTPUT:
![alt text](<Screenshot 2025-11-20 111610.png>)
![alt text](<Screenshot 2025-11-20 111639.png>)

### RESULT:
The web app successfully generates accurate text captions for uploaded images and displays them through a simple Gradio interface.