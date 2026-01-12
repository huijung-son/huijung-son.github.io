---
title: "Diagrams Install"
date: 2026-01-09 00:00:00
categories: [Reference, Install]
tags: [Diagrams]
description: ""
---

### Diagrams 링크

[https://diagrams.mingrammer.com/](https://diagrams.mingrammer.com/){: target="_blank"}

### installation 주의 사항

> diagrams requires Python 3.7 or higher, check your Python version first.

> diagrams uses Graphviz to render the diagram, so you need to install Graphviz to use it.

> macOS users using Homebrew can install Graphviz via brew install graphviz . Similarly, 
> Windows users with Chocolatey installed can run choco install graphviz or use Winget via winget install Graphviz.Graphviz -i.

[Homebrew Install]({% post_url 2025-12-03-reference-install-homebrew %})

### UV 가상환경에서 다이어그램 만들기

[UV Install]({% post_url 2025-12-02-reference-install-uv %})

```shell
vi diagrams.py
uv venv
source .venv/bin/activate
uv pip install diagrams
python diagrams.py
```
