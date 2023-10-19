# RealtimeGen for AI-painting based on Stable Diffuison

## Abstract
Recent advances in AIGC technology enable the rapid generation of high-quality images and draw the attention from painters. However, integrating the end-to-end image generation principle with the step-by-step human painting process remains challenging. Existing research has revealed the limitations of fully automated technology for professionals. We designed **RealtimeGen**, an interventable interactive image generation tool. In fact, it can be used for Adobe Photoshop. We notice the intrinsic step-by-step principle behind diffusion models, an advanced generation technology. By exposing the full generation process, RealtimeGen allows painters to integrate both AI generation and human painting process.

## Introduction
The algorithm consists of two main stages: normal generation and edited generation. In the normal generation stage, we can preview images of Stable Diffusion's intermediate stages (denoised prediction $\tilde I_0^t$ and intermediate generated image $\hat I_t$) using a fast approximation to visualize the low-resolution (64px) latent state. At the end of the normal generation stage, we can choose to download and edit the denoised prediction $\tilde I_0^t$ image at any desired time step. The edited image can then be re-encoded and noised at any timestep to produce $\tilde z_t$ for the edited generation stage, which allows for controlled generation. Notably, this idea is closely related to the paper[Universal Guidance for Diffusion Models(CVPR 2023)](https://arxiv.org/pdf/2302.07121.pdf). For future improvements, we discover that if the edited image, after being noised and encoded, remains same with the initial $\hat z_t$, the noise added to the edited image could become deterministic rather than randomly initialized. Taking DDIM as an example, the added noise can be represented as $\tilde \epsilon = \epsilon_{\theta}(z_t, t) - \sqrt{\alpha_t/(1 - \alpha_t)} \Delta z_0$, where $\Delta z_0$ denotes the corresponding change. This approach may offer advantages in preserving the structure and elements of the original image.

We have developed a Gradio interface that achieves this project, enabling users to easily and quickly try it out. Additionally, it can be utilized in Adobe Photoshop 2023. Of course, our goal is to enable free editing by integrating this algorithm as a Photoshop plugin. Furthermore, we offer both front-end demo and backend API call methods built on the FastAPI for reference. Our code is compatible with various Stable Diffusion model series, such as SDXL, SD2, and SDV1.5.

## Illustration
* app.py is a Gradio application that yields and visualizes preview images including denoised prediction $\tilde I_0^t$ and intermediate generated image $\hat I_t$ from a generator function while the pipeline is in progress. The UI is directly derived from Stability AI's Stable Diffusion Demo.
* preview_decoder.py provides the fast latent-to-RGB decoder function.
* item.py provides two request classes (**Item_ori** and **Item_edit**) used to store all the input form required for generating by the backend SD model.
* back_utils provides the necessary tool methods for backend generation, such as convert_image_to_base64, convert_base64_to_image, save_img, read_img.
* client.py provides a simple client demo which is used to send and receive requests to the backend API.
* generator_pipeline.py provides a DiffusionPipeline with a generate method to yield the latent data at each step inherited from StableDiffusionPipeline. It is nearly a strict refactoring of the StableDiffusionPipeline in 🧨diffusers 0.19.
* server.py provides the backend processing code based on 🧨FastAPI. It is responsible for receiving front-end requests, parsing the requests into proper input, calling the backend SD model, and returning the processed output results to the front-end.
  
## Run
```
python app.py
```
## Display

