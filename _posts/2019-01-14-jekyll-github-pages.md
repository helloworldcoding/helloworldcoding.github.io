---
layout: post
title: "use jekyll to generate github pages"
description: "a easy way to build your personal website"
tags: [jekyll, github pages]
image:
  path: /images/abstract-6.jpg
  feature: abstract-6.jpg
---


<div class="mermaid">
	graph LR
		node
		id[text in the box]
		id1(text in the round edges box)
		id2((text in the circle))
		id3>text in the label]
		id4{text in the rhombus box}
		node --> id1
		id1 --> id2
		id2 --> id3 
		id3 --> id4
		node --> id
</div>

<div class="mermaid">
	graph LR
	    id1(Start)-->id2(Stop)
	    style id1 fill:#f9f,stroke:#333,stroke-width:4px,width:70px,height:50px
		style id2 fill:#ccf,stroke:#f66,stroke-width:2px,stroke-dasharray: 5, 5
		linkStyle 0 stroke:#ff3,stroke-width:4px;

</div>

<div class="mermaid">
graph TB
    c1-->a2
    subgraph one
    a1-->a2
    end
    subgraph two
    b1-->b2
    end
    subgraph three
    c1-->c2
    end

</div>

{% mermaid %}
sequenceDiagram
    Alice->>Bob: Hello Bob, how are you?
    alt is sick
        Bob->>Alice: Not so good :(
    else is well
        Bob->>Alice: Feeling fresh like a daisy
    end
    opt Extra response
        Bob->>Alice: Thanks for asking
    end
{% endmermaid %}
