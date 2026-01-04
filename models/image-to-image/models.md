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

