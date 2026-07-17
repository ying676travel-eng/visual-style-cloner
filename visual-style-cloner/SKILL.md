---
name: visual-style-cloner
description: Reverse-engineer the visual style of a reference image, refine and confirm replacement content, build a spatially controlled prompt for ChatGPT image generation, generate or iteratively edit the image, and optionally write a 3–5 second image-to-video motion prompt for tools such as Jimeng or Kling after the user approves the image. Use when the user wants to clone a reference image's medium, texture, linework, palette, lighting, character language, typography, composition, or whitespace while applying new titles, panels, summaries, or layouts; also use for requests to turn a content outline into a similarly styled visual even when no reference image is provided.
---

# Visual Style Cloner

Turn a reference style and new content into a generated image through a gated workflow. Do not skip or combine the two user-confirmation gates.

## Required capability

Use the `imagegen` skill and its image-generation tool for every new image or image revision. Inspect any local reference image with the available image-viewing tool before analyzing or editing it. When the tool requires no follow-up text after generation, tell the user immediately before the tool call to reply with either “满意” or specific revision notes after viewing the result.

Read [references/prompt-template.md](references/prompt-template.md) only after the user has confirmed the content and supplied the aspect ratio.

## Workflow

### 1. Check inputs and reverse-engineer the style

Determine whether the user supplied:

- a reference image;
- replacement content or a rough draft;
- an image ratio.

If a reference exists, ignore the default style anchor. Inspect it deeply and infer:

1. medium and production method;
2. material and surface texture;
3. line weight, edge quality, and brush or stroke behavior;
4. color palette, saturation, contrast, and accent-color logic;
5. light direction, softness, shadow behavior, and depth;
6. character proportions, facial simplification, shapes, clothing, and props;
7. typography, hierarchy, alignment, and label treatment;
8. composition, grid, panel rhythm, margins, and approximate whitespace ratio;
9. negative traits: what the style explicitly is not.

Convert the findings into precise English image-prompt phrases. Always show the user a concise, structured summary of the reverse-engineered style before proceeding. Present observable conclusions, not hidden reasoning. Include:

- medium and texture;
- line work;
- color and lighting;
- character language;
- typography;
- composition and estimated whitespace ratio;
- negative traits;
- a final line of reusable English style keywords.

If no reference exists, use the default style anchor in the prompt template.

### 2. Show the style analysis, refine the content, and confirm — Gate 1

Extract only the semantic content that must appear in the image. Preserve the user's intended meaning while shortening text enough to render legibly.

Before drafting the image text, determine the middle-content structure from the semantic structure of the user's copy alone. Do not use the reference image or requested aspect ratio to decide the number of middle scenes or panels.

- Use **one complete scene** when the copy expresses one core situation, action, emotion, or proposition.
- Use **two independent panels** when the copy contains two steps, two parallel points, two objects, or a clear before/after contrast.
- Use **three independent panels** when the copy contains three steps, three parallel points, or a three-part process.
- When the copy contains more than three information units, group them into two or three coherent units without changing meaning. If meaningful grouping would lose essential information, flag the issue for user confirmation instead of overcrowding the image.
- Never invent extra panels merely to fill a layout.

The reference image controls visual style, character-design language, palette, texture, typography treatment, spacing rhythm, and whitespace. It does not control panel count. The aspect ratio controls only how the confirmed scene or panels are arranged.

In the same response, first show the structured visual-style analysis from Step 1, then send a compact content-confirmation draft containing:

- title;
- recommended middle structure: one complete scene, two panels, or three panels;
- a one-sentence reason based on the copy's information structure;
- for one scene: its core visual action and exact visible text;
- for multiple panels: each panel's label, core scene/message, and exact visible text;
- bottom summary;
- any text that must remain verbatim.

End by asking the user to confirm or revise the style understanding and content. Stop. Do not ask for the ratio in the same turn and do not generate the final prompt yet.

If essential content is missing, make the smallest reasonable draft from available context and mark the uncertain part for confirmation instead of asking many preliminary questions.

### 3. Ask for the aspect ratio — Gate 2

Only after the user confirms the content, ask exactly one concise question:

“请指定生成比例：横版 16:9、竖版 3:4，还是方版 1:1？”

Stop and wait. If the user already explicitly supplied a ratio earlier, restate it as the selected ratio and ask for a simple confirmation instead of asking them to choose again.

### 4. Build the final image prompt

After Gate 2, read the prompt template and produce the final prompt in its prescribed structure.

Requirements:

- Describe style, composition, characters, scenes, and constraints in English.
- Preserve all visible image text in Chinese and quote it exactly.
- Translate the observed whitespace ratio into concrete layout language.
- Force wide outer margins and internal padding. When multiple panels exist, also force wide panel gaps and independent visual regions.
- Include: `Generous whitespace`, `Wide margins`, `Elements isolated on white background`, `Clean layout`, and `Uncluttered`.
- For one complete scene, use one large coherent middle visual area and do not force it into a square panel.
- For two or three independent panels, keep each panel's visual area square (1:1) even when the overall canvas is not square.
- For landscape with two panels, use one row with two columns; with three panels, use one row with three columns.
- For vertical with two panels, stack them; with three panels, use a readable vertical stack or balanced 2+1 arrangement. Never squeeze three detailed panels into a narrow row.
- Use the confirmed semantic structure to determine panel count. Use aspect ratio and readability to determine arrangement; use the reference only to style the arrangement.
- Keep top, middle, and bottom sections visually separate with visible gaps.
- Add explicit negative constraints derived from the reference analysis.

Show the final prompt in one copyable text block. Then briefly tell the user that the image will be generated now and that, after viewing it, they should reply “满意” or send precise revision notes.

### 5. Generate and iterate

Immediately call the image-generation tool with the final prompt. Do not ask for another confirmation before generating.

If the user supplied one or more reference images, include the relevant reference image files in the image-generation call in addition to the final prompt. Do not rely on text description alone when the actual reference is available.

- When all reference images have local paths, pass them through the image tool's referenced-image-path mechanism.
- When reference images exist only in recent conversation context, include the smallest number of recent images that contains every required reference.
- Never use both image-inclusion mechanisms in the same call.
- State inside the generation prompt what each reference controls: visual style only, character identity, pose, composition, or another explicitly requested attribute.
- Unless the user asks to reproduce content, treat a style reference as a style source only. Do not copy its original wording, subjects, props, or layout details into the new image.
- Give the user's confirmed new content and layout priority over conflicting content in the reference image.
- If there is no reference image, generate from the final prompt alone.

On the next user turn:

- If the user gives revision notes, inspect the generated image when needed, edit the existing image rather than regenerating from scratch, and preserve every unmentioned detail. Include the current generated image as the edit target; retain the original reference as an additional style anchor when needed for consistency. Before each edit call, ask the user to reply “满意” or provide further revision notes after viewing it. Continue until approved.
- If the user says only that they are not satisfied, ask for the 1–3 most important changes. Do not guess broadly.
- If the user says the image is satisfactory, proceed to Gate 3.

### 6. Offer a motion prompt — Gate 3

After explicit approval, ask:

“需要我基于这张图，再写一版适用于即梦、可灵的 3–5 秒动态视频提示词吗？”

Stop and wait.

If the user agrees, write one ready-to-copy Chinese prompt based on the approved image. Default to 5 seconds unless the user specifies otherwise. Include:

- locked visual style, composition, text, and identity;
- one primary subject motion and no more than two subtle secondary motions;
- restrained camera movement;
- start state, motion progression, and end state;
- motion amplitude and timing;
- explicit constraints against new objects, text deformation, layout drift, flicker, morphing, and camera shake.

Favor small, coherent motion that makes the still image feel alive. Do not redesign the image or introduce new narrative content.

Before writing the motion prompt, count the distinct visual panels in the approved image's middle content area and branch accordingly:

- **One middle panel:** Animate it as one continuous scene. Do not add sequential panel appearance or disappearance.
- **Two or three middle panels:** Animate the panels sequentially in the intended reading order, normally left-to-right for a horizontal row and top-to-bottom for a vertical stack. Only one panel may be active at a time. For each panel: gently fade or scale in, play its small internal motion, hold briefly, then slowly fade out before the next panel appears. Allow only a short clean crossfade; do not let multiple panels compete on screen. Keep the title, bottom summary, background, and overall frame stable throughout.
- **Four or more middle panels:** Do not compress every panel into 3–5 seconds. Ask whether the user wants a longer video or multiple short clips before writing the motion prompt.

For multi-panel motion, allocate the available duration proportionally so every panel has a readable entrance, action, brief hold, and exit. Permit opacity and very subtle scale changes only at the panel level; do not rearrange panels or morph their internal text, characters, or geometry.

If the user declines, end the workflow without additional offers.

## Interaction rules

- Ask only one blocking question per turn.
- Do not collapse Gate 1 and Gate 2 into one question.
- Never generate before the content and ratio are confirmed.
- Show the concise visual-analysis conclusions by default, but never expose hidden chain-of-thought or lengthy reasoning.
- Treat Chinese spelling as exact artwork content. Never silently rewrite confirmed text.
- For edits, change only what the user names.
