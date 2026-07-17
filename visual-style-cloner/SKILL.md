---
name: visual-style-cloner
description: Reverse-engineer the visual style of a reference image, refine and confirm replacement content, build a spatially controlled prompt for Codex image generation, generate or iteratively edit the image, and optionally write a 3-5 second image-to-video motion prompt for tools such as Jimeng or Kling after the user approves the image. Use when the user wants to clone a reference image's medium, texture, linework, palette, lighting, character language, typography, composition, or whitespace while applying new titles, panels, summaries, or layouts; also use for requests to turn a content outline into a similarly styled visual even when no reference image is provided.
---

# Visual Style Cloner

Turn a visual reference plus new copy into a same-style generated image through a fixed, gated conversation. Keep user-facing messages short and direct; keep the detailed reasoning and prompt construction internal.

## Required capability

Use the `imagegen` skill and Codex image-generation tool for every new image or image revision. Inspect any local reference image with the available image-viewing tool before analyzing or editing it.

Read [references/interaction-templates.md](references/interaction-templates.md) when drafting user-facing stage messages. Read [references/prompt-template.md](references/prompt-template.md) only after the user has confirmed the content and supplied the aspect ratio.

## Conversation contract

- Move one stage at a time. Do not collapse content confirmation, ratio selection, and generation into one turn.
- Ask at most one blocking question per turn.
- If the user invokes the skill without enough inputs, greet them and ask for a reference image plus the copy they want illustrated. Also state that after receiving them, you will return a visual-style analysis and a content confirmation draft.
- Do not ask the user to structure the copy. Extract title, middle structure, visible text, and bottom summary yourself, then ask them to confirm or revise.
- Keep user-facing language concise. Do not explain internal prompt mechanics unless the user asks.
- Preserve confirmed Chinese text exactly. Never silently rewrite spelling, punctuation, or wording.
- Treat the reference image as a style source unless the user explicitly assigns another role.

## Workflow

### 1. Intake

Determine whether the user supplied:

- a reference image;
- replacement content or rough copy;
- an image ratio.

If both reference image and copy are present, proceed to analysis. If either is missing, ask only for the missing item.

### 2. Analyze style and confirm content - Gate 1

If a reference exists, inspect it deeply and infer:

1. medium and production method;
2. material and surface texture;
3. line weight, edge quality, and brush or stroke behavior;
4. color palette, saturation, contrast, and accent-color logic;
5. light direction, softness, shadow behavior, and depth;
6. character proportions, facial simplification, shapes, clothing, and props;
7. typography, hierarchy, alignment, and label treatment;
8. composition, grid, panel rhythm, margins, and approximate whitespace ratio;
9. negative traits: what the style explicitly is not.

Convert findings into precise English image-prompt phrases for later use. Show the user only a concise structured summary:

- medium and texture;
- line work;
- color and lighting;
- character language;
- typography;
- composition and estimated whitespace ratio;
- negative traits.

Extract only semantic content that must appear in the new image. Preserve intent while shortening text enough to render legibly.

Determine the middle-content structure from the user's copy alone:

- Use one complete scene when the copy expresses one core situation, action, emotion, or proposition.
- Use two independent panels when the copy contains two steps, two parallel points, two objects, or a before/after contrast.
- Use three independent panels when the copy contains three steps, three parallel points, or a three-part process.
- If the copy contains more than three information units, group them into two or three coherent units. If grouping would lose essential meaning, ask the user to resolve it.

The reference controls style, character-design language, palette, texture, typography treatment, spacing rhythm, and whitespace. It does not control panel count. The aspect ratio controls only how the confirmed scene or panels are arranged.

In the same response, show the style summary and a compact content-confirmation draft containing:

- title;
- recommended middle structure: one complete scene, two panels, or three panels;
- a one-sentence reason based on the copy's information structure;
- for one scene: its core visual action and exact visible text;
- for multiple panels: each panel's label, core scene/message, and exact visible text;
- bottom summary;
- any text that must remain verbatim.

End by asking the user to confirm or revise the style understanding and content. Stop. Do not ask for the ratio yet.

### 3. Ask for aspect ratio - Gate 2

Only after the user confirms the content, ask one concise question:

`请指定生成比例：横版 16:9、横版 4:3、竖版 3:4、方版 1:1，还是自定义比例？`

If the user already supplied a ratio earlier, restate it as the selected ratio and ask for simple confirmation.

### 4. Build the prompt and generate

After Gate 2, read the prompt template and produce the final prompt in its prescribed structure.

Internal generation contract:

- The final generation prompt must include the reverse-engineered style description from Gate 1, the confirmed new content, the selected ratio, layout decisions, and negative constraints.
- If the original reference image is available, include it in the image-generation call together with the final prompt. Do not rely on text description alone.
- State inside the prompt what the reference controls. For style-only references, transfer medium, texture, line behavior, color logic, lighting, character-design language, typography treatment, spacing rhythm, and whitespace ratio.
- Do not copy the reference image's original text, subjects, props, or exact layout unless explicitly requested.
- The confirmed new content and layout instructions override conflicting content visible in the reference image.

Prompt requirements:

- Describe style, composition, characters, scenes, and constraints in English.
- Preserve all visible image text in Chinese and quote it exactly.
- Translate the observed whitespace ratio into concrete layout language.
- Force wide outer margins and internal padding. When multiple panels exist, also force wide panel gaps and independent visual regions.
- Include `Generous whitespace`, `Wide margins`, `Elements isolated on white background`, `Clean layout`, and `Uncluttered`.
- For one complete scene, use one large coherent middle visual area and do not force it into a square panel.
- For two or three independent panels, keep each panel's visual area square (1:1) even when the overall canvas is not square.
- Use the confirmed semantic structure to determine panel count. Use aspect ratio and readability to determine arrangement; use the reference only to style the arrangement.
- Keep top, middle, and bottom sections visually separate with visible gaps.
- Add explicit negative constraints derived from the reference analysis.

Show the final prompt in one copyable text block. Then briefly tell the user that you will call the Codex built-in image-generation tool now and that, after viewing it, they should reply `满意` or send specific revision notes.

Immediately call the image-generation tool. Do not ask for another confirmation before generating.

### 5. Iterate or approve

On the next user turn:

- If the user gives revision notes, inspect the generated image when needed, edit the existing image rather than regenerating from scratch, and preserve every unmentioned detail. Include the current generated image as the edit target; retain the original reference as an additional style anchor when needed for consistency. Before each edit call, ask the user to reply `满意` or provide further revision notes after viewing it.
- If the user says only that they are not satisfied, ask for the 1-3 most important changes. Do not guess broadly.
- If the user says the image is satisfactory, proceed to Gate 3.

### 6. Offer a motion prompt - Gate 3

After explicit approval, ask:

`需要我基于这张图，再写一版适用于即梦、可灵的 3-5 秒动态视频提示词吗？`

Stop and wait.

If the user agrees, write one ready-to-copy Chinese prompt based on the approved image. Default to 5 seconds unless the user specifies otherwise. Include:

- locked visual style, composition, text, and identity;
- one primary subject motion and no more than two subtle secondary motions;
- restrained camera movement;
- start state, motion progression, and end state;
- motion amplitude and timing;
- explicit constraints against new objects, text deformation, layout drift, flicker, morphing, and camera shake.

Before writing the motion prompt, count the distinct visual panels in the approved image's middle content area:

- One middle panel: animate it as one continuous scene. Do not add sequential panel appearance or disappearance.
- Two or three middle panels: animate panels sequentially in the intended reading order. Only one panel may be active at a time. Keep the title, bottom summary, background, and overall frame stable throughout.
- Four or more middle panels: do not compress every panel into 3-5 seconds. Ask whether the user wants a longer video or multiple short clips before writing the motion prompt.

If the user declines, end the workflow without additional offers.
