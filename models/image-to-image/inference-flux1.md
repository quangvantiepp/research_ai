### script
```py

pip install -U \
  diffusers>=0.31.0 \
  accelerate>=1.0.1 \
  transformers>=4.45.2 \
  sentencepiece>=0.2.0 \
  torch>=2.4.1 \
  protobuf


import torch
import argparse
from diffusers import FluxImg2ImgPipeline
from diffusers.utils import load_image

def main():
    parser = argparse.ArgumentParser(description="FLUX.1-schnell Img2Img Inference")
    
    # Tham số cấu hình
    parser.add_argument("--input", type=str, required=True, help="Đường dẫn ảnh gốc")
    parser.add_argument("--prompt", type=str, required=True, help="Prompt mô tả")
    parser.add_argument("--strength", type=float, default=0.6, help="Độ biến đổi (0.0-1.0)")
    parser.add_argument("--steps", type=int, default=4, help="Schnell tối ưu nhất ở 1-4 bước")
    parser.add_argument("--output", type=str, default="flux_schnell_result.png")
    parser.add_argument("--seed", type=int, default=None)

    args = parser.parse_args()

    model_id = "black-forest-labs/FLUX.1-schnell"

    # 1. Khởi tạo Pipeline với kiểu dữ liệu bfloat16 (tối ưu cho A5000)
    pipe = FluxImg2ImgPipeline.from_pretrained(
        model_id, 
        torch_dtype=torch.bfloat16,
        use_safetensors=True
    )

    # 2. Tối ưu bộ nhớ cho 14GB VRAM
    # Nếu bị lỗi OOM, hãy dùng pipe.enable_model_cpu_offload() thay cho pipe.to("cuda")
    pipe.to("cuda")
    pipe.enable_vae_tiling() 

    # 3. Chuẩn bị ảnh
    init_image = load_image(args.input).convert("RGB")
    
    generator = None
    if args.seed is not None:
        generator = torch.Generator("cuda").manual_seed(args.seed)

    # 4. Chạy Inference
    print(f"Đang tạo ảnh với {args.steps} bước...")
    with torch.inference_mode():
        image = pipe(
            prompt=args.prompt,
            image=init_image,
            strength=args.strength,
            num_inference_steps=args.steps,
            guidance_scale=0.0,  # Schnell KHÔNG dùng guidance
            width=1024,
            height=1024,
            generator=generator
        ).images[0]

    image.save(args.output)
    print(f"Xong! Ảnh đã lưu tại: {args.output}")

if __name__ == "__main__":
    main()


    python script_name.py --input "my_photo.jpg" --prompt "oil painting style, vivid colors" --strength 0.5 --steps 4

```

Tại sao Script này phù hợp với bạn?
Guidance Scale = 0.0: Đây là điểm bắt buộc đối với dòng Schnell. Nếu bạn để mặc định (7.5), ảnh sẽ bị cháy sáng hoặc nhiễu nặng.

Số bước (Steps): Khác với các model khác cần 20-50 bước, Schnell chỉ cần 4 bước để cho ra chất lượng ảnh hoàn hảo. Điều này giúp card A5000 của bạn xuất ảnh gần như tức thì (dưới 5 giây).

Memory Management:

pipe.enable_vae_tiling(): Giúp bạn xử lý ảnh Image-to-Image ở độ phân giải cao mà không lo phần VAE làm tràn bộ nhớ 14GB khi giải mã ảnh cuối cùng.

torch.bfloat16: Tận dụng lõi Tensor trên A5000 để tăng tốc độ tính toán.