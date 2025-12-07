## Về Image-to-Image (I2I)
Thực tế, hầu hết các model Text-to-Image ở trên đều có thể thực hiện Image-to-Image. Tuy nhiên, để làm I2I chuyên sâu (giữ cấu trúc, dáng pose), bạn cần các Adapter Models đi kèm.

Dưới đây là các module không thể thiếu cho I2I:

ControlNet: Dùng để kiểm soát dáng người, nét vẽ, độ sâu ảnh.

Repo SD1.5: lllyasviel/ControlNet-v1-1

Repo SDXL: xinsir/controlnet-union-sdxl-1.0 (Mạnh nhất cho SDXL)

Repo Flux: XLabs-AI/flux-controlnet-collections (Đang phát triển)

IP-Adapter: Dùng để copy phong cách hoặc khuôn mặt từ ảnh này sang ảnh khác.

Repo: h94/IP-Adapter