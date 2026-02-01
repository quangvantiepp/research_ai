## Về Image-to-Image (I2I)
Thực tế, hầu hết các model Text-to-Image ở trên đều có thể thực hiện Image-to-Image. Tuy nhiên, để làm I2I chuyên sâu (giữ cấu trúc, dáng pose), bạn cần các Adapter Models đi kèm.

Dưới đây là các module không thể thiếu cho I2I:

ControlNet: Dùng để kiểm soát dáng người, nét vẽ, độ sâu ảnh.

Repo SD1.5: lllyasviel/ControlNet-v1-1

Repo SDXL: xinsir/controlnet-union-sdxl-1.0 (Mạnh nhất cho SDXL)

Repo Flux: XLabs-AI/flux-controlnet-collections (Đang phát triển)

IP-Adapter: Dùng để copy phong cách hoặc khuôn mặt từ ảnh này sang ảnh khác.

Repo: h94/IP-Adapter

trùng với text to image



### image to image:
#### Để chạy FLUX trên 14GB VRAM mà vẫn còn dư bộ nhớ cho các tác vụ khác, bạn nên dùng thư viện bitsandbytes để load bản NF4.
-> lllyasviel/flux1-dev-bnb-nf4

-> black-forest-labs/FLUX.1-schnell

#### Model này chạy rất mượt ở định dạng FP16 trên 14GB VRAM.
-> stabilityai/stable-diffusion-3.5-medium



#### Model flux2
1. Link tải các thành phần (Hugging Face)
Bạn cần tải 3 thành phần chính để chạy trong ComfyUI:

Model Checkpoint (Bản nén GGUF):

Lựa chọn 1 (Klein 4B - Nhẹ nhất): unsloth/FLUX.2-klein-4B-GGUF (Nên tải bản Q4_K_M khoảng 2.6GB, chạy cực nhanh trên GPU 8GB VRAM).

Lựa chọn 2 (Dev 32B - Chất lượng cao nhất): unsloth/FLUX.2-dev-GGUF (Tải bản Q3_K_S hoặc Q4_K_S nếu bạn có 12GB-16GB VRAM).
- https://huggingface.co/unsloth/FLUX.2-dev-GGUF/tree/main

VAE (Giải mã ảnh):

Tải file ae.safetensors từ: black-forest-labs/FLUX.1-dev (Dùng chung VAE với bản FLUX.1).

CLIP/Text Encoder (Bộ hiểu ngôn ngữ):


Tải bản FP8 để tiết kiệm RAM: comfyanonymous/flux_text_encoders.
- https://huggingface.co/comfyanonymous/flux_text_encoders/tree/main

2. Workflow Image-to-Image cụ thể trên ComfyUI
Để chạy Image-to-Image (I2I) với FLUX.2, bạn cần cài đặt ComfyUI-GGUF và ComfyUI-Manager. Workflow I2I của FLUX.2 hơi khác so với Stable Diffusion cũ vì nó sử dụng cơ chế Reference Latent.

Bước 1: Thiết lập các Node cơ bản
Load GGUF Model: Dùng node Unet Loader (GGUF) để nạp file model Klein hoặc Dev đã tải.

Load CLIP: Dùng node DualCLIPLoader (chọn file clip_l và t5xxl_fp8).

Load VAE: Dùng node VAE Loader.

Bước 2: Xử lý ảnh đầu vào (Cấu trúc Image-to-Image)
Load Image: Kéo ảnh bạn muốn biến đổi vào.

VAE Encode: Nối ảnh vào node này để chuyển sang dạng Latent.

FLUX.2 Reference Node (Quan trọng nhất):

Khác với I2I cũ dùng Denoise, FLUX.2 dùng một node gọi là FLUX Reference hoặc nối trực tiếp vào Latent Composite.

Tuy nhiên, cách đơn giản nhất cho người mới là dùng node "SamplerCustom" kết hợp với "BasicScheduler". Bạn nối Latent từ ảnh gốc vào, và chỉnh chỉ số denoise (từ 0.4 - 0.7).

Bước 3: Workflow "Klein" (Siêu tốc)
Dòng FLUX.2 [klein] được thiết kế để Image-to-Image chỉ trong 1-4 bước (Steps):

Sampler: euler

Scheduler: simple hoặc sgm_uniform

Steps: 1 đến 4 bước (đừng để cao hơn sẽ bị cháy ảnh).

Guidance: 3.5 (với bản Dev) hoặc 1.0 (với bản Klein).

3. Mẹo để máy yếu chạy mượt
Dùng bản 4-bit (Q4): Chất lượng hầu như không khác biệt với FP16 nhưng giảm 70% dung lượng VRAM.

Sử dụng Command Line: Khi chạy ComfyUI, hãy thêm đối số --lowvram hoặc --pitched-vram vào file .bat khởi động.

Kích thước ảnh: Với máy VRAM thấp, hãy bắt đầu ở độ phân giải 896x896 hoặc 1024x1024, không nên cao hơn ngay lập tức.

### chạy cũng khá ok
- https://docs.comfy.org/tutorials/flux/flux-2-klein