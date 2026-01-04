## script
```py
import torch
import argparse
import os
from diffusers import FluxImg2ImgPipeline
from diffusers.utils import load_image

def main():
    # 1. Thiết lập các tham số dòng lệnh
    parser = argparse.ArgumentParser(description="Chạy Inference FLUX.1 [dev] NF4 Img2Img")
    
    # Tham số bắt buộc
    parser.add_argument("--input", type=str, required=True, help="Đường dẫn file ảnh gốc hoặc URL")
    parser.add_argument("--prompt", type=str, required=True, help="Mô tả nội dung muốn thay đổi")
    
    # Tham số tùy chọn (có giá trị mặc định)
    parser.add_argument("--strength", type=float, default=0.6, help="Độ biến đổi ảnh (0.0 đến 1.0). Mặc định 0.6")
    parser.add_argument("--steps", type=int, default=25, help="Số bước chạy (inference steps). Mặc định 25")
    parser.add_argument("--guidance", type=float, default=3.5, help="Độ bám sát prompt (Guidance Scale). Mặc định 3.5")
    parser.add_argument("--output", type=str, default="output.png", help="Tên file ảnh kết quả")
    parser.add_argument("--seed", type=int, default=None, help="Cố định seed để tái tạo kết quả")

    args = parser.parse_args()

    # 2. Khởi tạo Model
    model_id = "lllyasviel/flux1-dev-bnb-nf4"
    print(f"--- Đang tải model: {model_id} ---")
    
    pipe = FluxImg2ImgPipeline.from_pretrained(
        model_id,
        torch_dtype=torch.bfloat16
    )
    
    # Tối ưu cho VRAM 14GB
    pipe.to("cuda")
    pipe.enable_vae_tiling()

    # 3. Xử lý ảnh đầu vào
    print(f"--- Đang xử lý ảnh: {args.input} ---")
    init_image = load_image(args.input).convert("RGB")
    init_image = init_image.resize((1024, 1024)) # Resize để tối ưu cho Flux

    # Cố định seed nếu có
    generator = None
    if args.seed is not None:
        generator = torch.Generator("cuda").manual_seed(args.seed)

    # 4. Chạy Inference
    print(f"--- Đang tạo ảnh với Prompt: {args.prompt} ---")
    with torch.inference_mode():
        image = pipe(
            prompt=args.prompt,
            image=init_image,
            strength=args.strength,
            num_inference_steps=args.steps,
            guidance_scale=args.guidance,
            generator=generator,
            width=1024,
            height=1024
        ).images[0]

    # 5. Lưu kết quả
    image.save(args.output)
    print(f"--- ĐÃ XONG! Ảnh lưu tại: {args.output} ---")

if __name__ == "__main__":
    main()

python inference_flux.py --input "anh_goc.jpg" --prompt "A futuristic soldier in cyberpunk city"

pip install -U \
  diffusers>=0.31.0 \
  accelerate>=0.34.0 \
  transformers>=4.45.0 \
  bitsandbytes>=0.43.3 \
  sentencepiece>=0.2.0 \
  peft>=0.12.0 \
  torch>=2.4.0

```

````sh
Giải thích các tham số quan trọng:Tham sốÝ nghĩaLời khuyên cho A5000
--strengthKiểm soát mức độ giữ lại ảnh gốc.Dùng 0.4 - 0.6 để giữ cấu trúc, >0.8 để sáng tạo mới.
--stepsSố bước lấy mẫu.FLUX [dev] cần ít nhất 20 bước để sắc nét.
--guidanceMức độ tuân thủ Prompt.FLUX thường hoạt động tốt nhất ở mức 3.0 - 4.5.
enable_vae_tiling()Chia nhỏ ảnh khi decode.Rất quan trọng để không bị lỗi bộ nhớ khi xuất ảnh 1024px trở lên trên 14GB VRAM.
```