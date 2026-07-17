# Image Prompt Template

Use this reference only after the content and aspect ratio are confirmed.

## Default style anchor

Use only when no reference image is supplied:

`Minimalist high-resolution editorial illustration, clean flat vector art, pure white background, smooth rounded black outlines, simplified friendly characters, restrained warm palette with one vivid accent color, no gradients, no heavy shading, crisp Chinese typography, abundant negative space.`

## Reverse-engineered style vocabulary

Build a compact style description from the observed reference:

- **Medium:** marker sketch, watercolor illustration, paper collage, flat vector art, clay-like 3D render, cinematic photography, etc.
- **Texture:** visible paper grain, dry brush, smooth plastic, soft felt, glossy ceramic, clean digital surface, etc.
- **Line work:** uniform rounded outlines, tapered ink strokes, wobbly hand-drawn contours, no outlines, crisp geometric edges, etc.
- **Color and light:** palette, accent logic, saturation, contrast, light direction, shadow softness, and depth.
- **Characters:** proportions, face construction, gesture language, clothing simplification, recurring props.
- **Typography:** type family impression, weight, spacing, hierarchy, alignment, and decoration.
- **Composition:** grid, framing, scale relationships, padding, panel spacing, and approximate whitespace percentage.
- **Not:** crowded, messy, dark, photorealistic, overly complex, heavy shadows, gradients, touching panels, edge-clipped elements, or other traits contradicted by the reference.

Avoid empty adjectives such as “beautiful” or “high-end” unless paired with observable visual evidence.

## Reference image usage

When an actual reference image is available, attach it to the image-generation call and add the following block near the beginning of the final prompt:

```text
REFERENCE IMAGE ROLE:
- Use the attached reference image as the authoritative source for: [visual style / character identity / pose / composition — list only confirmed roles].
- For a style-only reference, transfer its medium, texture, line behavior, color logic, lighting, character-design language, typography treatment, spacing rhythm, and whitespace ratio.
- Do not copy the reference image's original text, subject matter, props, or exact layout unless explicitly requested.
- The confirmed new Chinese content and layout instructions below override any conflicting content visible in the reference image.
```

If multiple references are attached, identify them by order and assign one unambiguous role to each. If no reference image is available, omit this block and use the default style anchor.

## Final output format

```text
Module [number]: [module title]

[Style Description]:
[Precise reverse-engineered English style keywords, including medium, texture, line work, palette, lighting, character language, typography, and whitespace ratio. If no reference exists, insert the default style anchor.]

[If a reference image is attached, insert the REFERENCE IMAGE ROLE block here.]

Composition Modifiers: Minimalist composition, high negative space, generous whitespace around elements, wide margins, ample internal padding, elements isolated on white background, clean layout, uncluttered, clear separation between every visual region.

ASPECT RATIO & LAYOUT:
- Ratio: [confirmed ratio]
- Structure: 3 distinct sections — Top, Middle, Bottom — with clearly visible gaps.
- Outer safe area: Keep all content comfortably inside the canvas with wide margins; nothing touches or approaches the edges.
- Middle-content structure: [one complete scene / two independent panels / three independent panels], determined only from the confirmed copy's information structure.

1. TOP: Title text "[confirmed Chinese title]". [Reference-matched font style, hierarchy, color, and alignment]. Keep generous empty space around the title.

2. MIDDLE: Use exactly one of the following confirmed structures.

   A. ONE COMPLETE SCENE:
   One large, coherent visual scene spanning the middle area. Do not divide it into cards or square panels. [English description of the unified setting, subjects, action, relationships, and focal hierarchy]. Render exact Chinese text: "[Chinese text]".

   B. TWO INDEPENDENT PANELS:
   Use [one row x two columns for landscape / two stacked panels for vertical] with wide spacing. Each panel is a clearly bounded 1:1 square visual area with ample internal padding and an independent focal subject.
   - Panel 1: [English scene description]. Render exact Chinese label/text: "[Chinese text]".
   - Panel 2: [English scene description]. Render exact Chinese label/text: "[Chinese text]".

   C. THREE INDEPENDENT PANELS:
   Use [one row x three columns for landscape / readable vertical stack or balanced 2+1 grid for vertical] with wide spacing. Each panel is a clearly bounded 1:1 square visual area with ample internal padding and an independent focal subject.
   - Panel 1: [English scene description]. Render exact Chinese label/text: "[Chinese text]".
   - Panel 2: [English scene description]. Render exact Chinese label/text: "[Chinese text]".
   - Panel 3: [English scene description]. Render exact Chinese label/text: "[Chinese text]".

   Use only the confirmed branch. Delete the other two branches from the actual generation prompt. Do not add panels based on the reference image or aspect ratio.

3. BOTTOM: Summary text "[confirmed Chinese summary]". [Reference-matched font style and placement]. Keep it visually separate from the middle panels.

TEXT RENDERING:
- All visible text must be Chinese.
- Render every quoted Chinese string exactly as written, with correct characters and punctuation.
- Keep text crisp, legible, and unobstructed; do not invent additional words.

NEGATIVE CONSTRAINTS:
- No crowded composition, no touching or overlapping panels, no edge clipping, no insufficient padding, no unintended objects, no duplicated subjects, no garbled Chinese text, no altered confirmed wording.
- [Add reference-specific negative traits.]
```

The copy's information structure determines whether the middle is one scene, two panels, or three panels. The reference image never determines panel count, and the aspect ratio determines arrangement only. Do not fabricate panels merely to fill space. For more than three semantic units, group them into two or three coherent units before using this template; if grouping would lose essential meaning, ask the user to resolve it first.

## Motion prompt format

After the user approves the image and opts in, output:

```text
【3–5 秒动态视频提示词】
以当前图片为唯一视觉基准，完整锁定原图的画风、人物形象、配色、文字内容、排版结构与画面比例。时长 [3–5] 秒。

起始状态：[describe the exact still-image starting state].
主体动作：[one clear, small-amplitude motion with direction and timing].
辅助动态：[up to two subtle environmental or decorative motions].
镜头：[static / very slow push-in / subtle lateral move], movement is smooth and restrained.
结束状态：[stable end pose, preferably compatible with a seamless loop when appropriate].

严格限制：不新增人物或物体；不修改、重绘或扭曲任何中文文字；不改变人物身份、五官、服装、比例和画风；不改变版式和元素位置；避免闪烁、抖动、跳帧、融化、变形、穿模、残影、镜头剧烈运动和背景漂移。
```

### Conditional middle-panel motion

Insert exactly one of the following blocks after the duration line.

For one middle panel:

```text
中间区域为单一完整画面，不使用分画面依次出现或消失的效果。让主体动作和辅助动态在同一场景中连续、自然地完成。
```

For two or three middle panels:

```text
中间区域包含 [N] 个独立画面，必须按照 [left-to-right / top-to-bottom / confirmed reading order] 依次动态展示，并且同一时间只突出一个画面。

画面 1：[gentle fade/scale in] → [small internal motion] → [brief readable hold] → [slow fade out].
画面 2：在画面 1 基本消失后再出现；[gentle fade/scale in] → [small internal motion] → [brief readable hold] → [slow fade out].
[Continue the same pattern for panel 3 when present.]

转场只允许短暂、干净的淡入淡出，不允许多个画面同时抢占视觉中心。标题、底部总结、背景和整体画框始终固定、清晰、稳定。只允许整个分画面发生透明度变化和极轻微缩放；分画面内部的文字、人物、图形和相对位置不得变形或漂移。
```

For four or more middle panels, do not produce a cramped 3–5 second sequence. Ask the user to choose a longer duration or multiple clips first.
