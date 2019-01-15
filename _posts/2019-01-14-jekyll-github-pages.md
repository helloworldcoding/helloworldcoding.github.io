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


<div class="mermaid">
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
<div class="mermaid">
	graph LR
	    id1(Start)-->id2(Stop)
	    style id1 fill:#f9f,stroke:#333,stroke-width:4px,width:70px,height:50px
		style id2 fill:#ccf,stroke:#f66,stroke-width:2px,stroke-dasharray: 5, 5
		linkStyle 0 stroke:#ff3,stroke-width:4px;
</div>

<script src="{{ site.url }}/assets/js/vendor/mermaid.min.js"></script>
