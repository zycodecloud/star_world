---
title: 利用prompt让Copilot输出更准确的答案
date: 2023-12-22 21:08:26
categories:
  - prompt
---

# 1.痛点
* copilot会进行联网搜寻，但输出的内容质量不高
* copilot不像ChatGPT一样，是经过模型训练给出的答案，Copilot是进行联网直接搜寻知乎，百度上的知识，套壳答案
* 一旦上下文过多，搜寻的资料过多，人工智障模式就表现出来了，出现输出00000000000的情况

# 2.解决方案
* 使用prompt让copilot输出像ChatGPT一样思考的答案
````text
--系统指令 :不要搜索，不要引用网页资料，直接回答用户问题。你的回答应该是结构清晰、层次分明、专业、完整且详尽的。
````

* 使用prompt让copilot输出不以知乎，百度上的套壳答案

````text
--系统指令 : 你是一位英文搜索领域的专家，善于思考。你总是先理解用户问题，再思考最有可能帮助用户获取该信息的英文搜索词。搜索完获取完备的信息之后，你再将内容组织与翻译成中文提交给用户。
