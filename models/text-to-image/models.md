# AI Models List

A. Cấu hình Thấp (Low-End)
VRAM yêu cầu: 4GB - 8GB.

Đặc điểm: Tốc độ nhanh, cộng đồng lớn, nhiều fine-tune models.

Kiến trúc chính: Stable Diffusion 1.5 (SD1.5).

| Tên Model         | Repo ID (Hugging Face)                        | Dataset chính      | Mô tả                                                                 |
|-------------------|-----------------------------------------------|--------------------|-----------------------------------------------------------------------|
| Stable Diffusion 1.5 | runwayml/stable-diffusion-v1-5               | LAION-5B (Subset)  | Model huyền thoại, nhẹ nhất, nhiều resources (LoRA/ControlNet) nhất. |
| DreamShaper 8     | Lykon/dreamshaper-8                          | Fine-tuned (Mixed) | Model SD1.5 được tinh chỉnh để tạo ảnh nghệ thuật/3D tốt hơn bản gốc. |
| Realistic Vision  | SG161222/Realistic_Vision_V6.0_B1_noVAE      | Fine-tuned (Photo) | Chuyên tạo ảnh người thật (photorealistic) cực tốt trên nền tảng SD1.5. |
| CyberRealistic    | cyberdelia/CyberRealistic_V5                 | Fine-tuned (Mixed) | Chuyên ảnh chân dung thực tế. |



## Cấu hình Trung bình (Mid-Range):
    - VRAM yêu cầu: 8GB - 12GB (Có thể chạy 16GB để mượt hơn).
    - Đặc điểm: Độ phân giải gốc cao hơn (1024x1024), chi tiết tốt hơn, hiểu prompt tốt hơn.
    - Kiến trúc chính: Stable Diffusion XL (SDXL), Playground.


| Tên Model      | Repo ID (Hugging Face)                     | Dataset chính       | Mô tả                                                                 |
|----------------|--------------------------------------------|---------------------|-----------------------------------------------------------------------|
| SDXL Base 1.0  | stabilityai/stable-diffusion-xl-base-1.0   | LAION-5B + Internal | Bước nhảy vọt về chất lượng so với SD1.5. Cần Refiner để tối ưu.     |
| Juggernaut XL  | RunDiffusion/Juggernaut-XL-v9             | Fine-tuned (SDXL)   | Một trong những model SDXL tốt nhất hiện nay cho ảnh chụp thực tế.   |
| Playground v2.5| playgroundai/playground-v2.5-1024px-aesthetic | Internal/Synthetic  | Màu sắc và ánh sáng rất đẹp, vượt trội SDXL gốc ở mảng thẩm mỹ.      |
| Animagine XL 3.1| cagliostrolab/animagine-xl-3.1            | Danbooru + Internal | Model tốt nhất cho phong cách Anime trên nền tảng SDXL.              |

## Cấu hình Cao (High-End & SOTA)
   - VRAM yêu cầu: 16GB - 24GB+ (Hoặc 40GB+ cho training).

   - Đặc điểm: Chất lượng hiện đại nhất, render chữ (text) chuẩn, tuân thủ prompt phức tạp cực tốt.

   - Kiến trúc chính: Flux, Stable Diffusion 3.

| Tên Model         | Repo ID (Hugging Face)                        | Dataset chính      | Mô tả                                                                 |
|-------------------|-----------------------------------------------|--------------------|-----------------------------------------------------------------------|
| FLUX.1 [dev]      | black-forest-labs/FLUX.1-dev                 | Private/Synthetic  | Vua hiện tại. Tạo ảnh cực nét, tay chân chuẩn, viết chữ đúng chính tả. Rất nặng. |
| FLUX.1 [schnell]  | black-forest-labs/FLUX.1-schnell             | Private/Synthetic  | Bản rút gọn của Flux, nhanh hơn (4 steps) nhưng chất lượng thấp hơn bản dev một chút. |
| Stable Diffusion 3| stabilityai/stable-diffusion-3-medium-diffusers | Private/Synthetic  | Bản mới nhất của StabilityAI, hiểu prompt tốt nhưng đôi khi bị lỗi giải phẫu (anatomy). |
| PixArt-Sigma      | PixArt-alpha/PixArt-Sigma-XL-2-1024-MS       | LAION + Internal   | Model Transformer nhẹ hơn Flux nhưng chất lượng rất cao, tuân thủ prompt tốt. |
