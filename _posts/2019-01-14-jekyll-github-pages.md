---
layout: post
title: "use jekyll to generate github pages"
description: "a easy way to build your personal website"
tags: [jekyll, github pages]
image:
  path: /images/abstract-6.jpg
  feature: abstract-6.jpg
---


{% mermaid %}
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
{% endmermaid %}

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


{% mermaid %}

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

{% endmermaid %}

{% mermaid %}
	graph LR
		A1 --> B1
		A2 --- B2
		C --text in the link --- B3
		D ---|text_on_link| B4
		E -->|text| B5
		F -.-> B6
		F -.->|text in line| Te
		H -.->|text| Note
		N1 ==>|text| N3
		Node["dd#quot\;"]
		Node1["#quot;A dec char:#9829;"]
		Node === Node1
{% endmermaid %}
{% mermaid %}
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
{% endmermaid %}

{% mermaid %}
	graph LR
	    id1(Start)-->id2(Stop)
	    style id1 fill:#f9f,stroke:#333,stroke-width:4px,width:70px,height:50px
		style id2 fill:#ccf,stroke:#f66,stroke-width:2px,stroke-dasharray: 5, 5
		linkStyle 0 stroke:#ff3,stroke-width:4px;
{% endmermaid %}

