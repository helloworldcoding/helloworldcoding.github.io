---
layout: post
title: "A devops flow"
description: "Think about how to make our work more easily"
mermaid: 1
---

<div class="mermaid">
graph TB
	
	subgraph devops
		test(("fa:fa-users Test"))
		dev(("fa:fa-user Dev"))
		ops(("Ops"))
		branch("branch")

		dev -->|创建分支| branch
		branch -->|jenkins| jenkins["jenkins"]
	end
	
	tes1t["fa:fa-camera-retro:5x Camera"]

	jenkins["fa:fa-futbol:fa-5x Iggool"]

     classDef green fill:#9f6,stroke:#333,stroke-width:2px;
     classDef orange fill:#f96,stroke:#333,stroke-width:4px;
     class sq,e green
     class di orange

</div>




