# Seedance Video Prompt — Baseline (No Skill File)

## Task Analysis

- **Input**: Product photo of white ceramic coffee cup (2 reference images)
- **Desired output**: 10-second product showcase ad
- **Style**: Minimalist, premium feel, black background
- **Aspect ratio**: 16:9 horizontal
- **Key moments**: Rotation entrance → texture detail → hot coffee pour with steam

## Recommended Generation Plan

Since Seedance 2.0 supports a maximum of ~15 seconds per generation, a 10-second video fits within a single generation. However, for higher quality control over distinct phases, consider splitting into 2 clips and stitching:

- **Clip 1 (0-6s)**: Cup rotation entrance + texture showcase
- **Clip 2 (6-10s)**: Coffee pouring with steam

Below is the single-generation prompt approach:

---

## Video Prompt (Single Generation)

```
@图片1 @图片2

纯黑背景，极简风格产品广告。一只白色陶瓷咖啡杯从画面中央缓缓旋转出场，杯身在柔和的侧光照射下逐渐显现，表面光洁细腻。镜头从中景缓慢推近，展示杯身精致的纹理细节和釉面质感。随后镜头微微上移，热咖啡从画面上方缓缓倒入杯中，液面荡漾，杯口升腾起白色蒸汽，氛围高级优雅。全程纯黑背景，光影对比强烈，画面干净极简。电影质感，4K画质。
```

---

## Prompt Structure Explanation

1. **Reference images placed first**: `@图片1` (product front view) and `@图片2` (texture detail) are placed at the beginning of the prompt so the model can use them as visual grounding for the cup's appearance and texture.

2. **Scene setup**: "纯黑背景，极简风格产品广告" — establishes the pure black background and minimalist ad aesthetic upfront.

3. **Phase 1 — Rotation entrance**: "白色陶瓷咖啡杯从画面中央缓缓旋转出场" — describes the cup rotating into frame with soft side lighting gradually revealing it.

4. **Phase 2 — Texture detail**: "镜头从中景缓慢推近，展示杯身精致的纹理细节和釉面质感" — camera push-in to showcase the ceramic texture and glaze, leveraging @图片2.

5. **Phase 3 — Coffee pour**: "热咖啡从画面上方缓缓倒入杯中，液面荡漾，杯口升腾起白色蒸汽" — the climactic moment with hot coffee pouring and visible steam.

6. **Atmosphere & quality keywords**: "光影对比强烈，画面干净极简，电影质感，4K画质" — quality boosters for premium cinematic look.

## Parameters

| Setting | Value |
|---------|-------|
| 画面比例 | 16:9 横屏 |
| 时长 | 10秒 |
| 参考图 | @图片1（产品正面）、@图片2（杯身纹理） |
| 风格 | 极简、高级感、电影质感 |

## Notes

- This baseline prompt uses a linear narrative structure describing temporal phases in sequence.
- The `@图片` references are placed at the start for the model to anchor visual identity.
- No specific Seedance-optimized prompt engineering techniques were applied — this relies on general Chinese video generation prompt conventions and base knowledge of the platform's capabilities.
