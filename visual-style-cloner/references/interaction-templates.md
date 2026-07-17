# Interaction Templates

Use these as short user-facing templates. Adjust the content, but keep the interaction stage and blocking question unchanged.

## Stage 1: Missing inputs

When the skill is invoked without enough inputs:

```text
你好，我可以帮你把参考图的视觉风格迁移到新的配图里。

请发我两样东西：
1. 一张想分析的参考图
2. 这次想做成配图的文案内容

收到后，我会先给你一版视觉风格分析和内容确认稿。
```

If only the image is missing:

```text
请再发一张想参考的图片。收到后，我会输出视觉风格分析和内容确认稿。
```

If only the copy is missing:

```text
请再发这次想做成配图的文案内容。收到后，我会输出视觉风格分析和内容确认稿。
```

## Stage 2: Style analysis and content confirmation

```text
我先确认两件事：参考图风格，以及这次要画进图里的内容。

视觉风格分析：
- 媒介/质感：
- 线条：
- 色彩/光影：
- 人物语言：
- 字体/文字：
- 构图/留白：
- 不要出现：

内容确认稿：
- 标题：
- 中间结构：
- 原因：
- 画面内容：
- 底部总结：
- 需要原样保留的文字：

请确认这版内容是否准确。需要改的话，直接告诉我改哪里。
```

## Stage 3: Ratio

```text
请指定生成比例：横版 16:9、横版 4:3、竖版 3:4、方版 1:1，还是自定义比例？
```

If the ratio was already provided:

```text
我会按你前面指定的 [ratio] 来生成。确认吗？
```

## Stage 4: Final prompt and generation

```text
已确认图片比例。下面是本次最终生图 Prompt，我现在会调用 Codex 内置生图工具生成图片。

[final prompt block]

图片生成后，请回复“满意”，或告诉我需要修改的地方。
```

## Stage 5: Revision

If revision notes are specific:

```text
收到，我会只修改你提到的部分，其余内容保持不变。改完后请回复“满意”，或继续告诉我要改哪里。
```

If the user only says they are not satisfied:

```text
请告诉我最想改的 1-3 个地方，比如标题、人物动作、构图、颜色或留白。我会按这些点继续修改。
```

## Stage 6: Motion prompt

After approval:

```text
需要我基于这张图，再写一版适用于即梦、可灵的 3-5 秒动态视频提示词吗？
```

If accepted, output only one ready-to-copy motion prompt unless the user asks for explanation.
