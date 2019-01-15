---
layout: post
title: "A breif introduction to Mermiad"
description: "write code to make some beautiful flowcharts"
tags: [flowcharts,Meriad,UML,viso]
image:
  path: /images/abstract-6.jpg
  feature: abstract-6.jpg
---

不用点点点,编写一些代码，生成想要的流程图，可以提高工作效率。应该修改代码的效率，远远要高于鼠标点点点。

### 流程图 flowcharts

* graph方向类型

	* TB  - top bottom  从上到下 TD和TB一样
	* BT  - bottom top  从下到上
	* RL  - right left  从右到左
	* LR  - left right  从左到右

* node 节点类型

```
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
```

* link 线条类型

	<>左右箭头，|text|在link上加文字， \-\-\- 实线，=== 粗线，  \-.\-点线
	* A \-\-> B	带箭头的线条
	* A \-\-\- B	不带箭头，实线
	* C \-\-text in the link \-\-\- B	线条上的文字
	* D \-\-\-\|text on the link\| B	带文字的线条，同上
	* E \-\- text onlink arrow \-\-> B	带箭头的线条，同时线条上有文字
	* E \-\-\->\|text on linkarrwo\|B	同上
	* F \-.\-> B	点线带箭头
	* F \-.\- B	点线,不带箭头
	* F \-.\-\|text\|Note
	* F \-.text.\-Note
	* N1 ==> N2	粗线
	* N3 ==>\|text\|N4

	* 特殊字符
		* 如果想输出特殊字符，比如( [ 等, 要用**双引号**
		* Node["this is (txxt)"]  用双引号把内容包起来，这样就能输出括号等特殊字符了。
		* 同时双引号内的html实体会被转义输出,如#quot;会转义为" #9829;会转义为一个心形

* subgraphs 子图

```
	subgraph title
		graph definition
	end
```
子图demo

```
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
```

* node和link的样式
	* link没有id，所以按照我们定义的顺序来标识link,第一个为0
	* node节点的大小，可以通过width和height来设置

```
graph LR
	id1(Start)-->id2(Stop)
	style id1 fill:#f9f,stroke:#333,stroke-width:4px,width:100px,height:50px
	style id2 fill:#ccf,stroke:#f66,stroke-width:2px,stroke-dasharray: 5, 5
	linkStyle 1 stroke:#ff3,stroke-width:4px;
```

###  如何把mermaid集成到jekyll中。

* 下面的方法在本地可以，但是github不可以

	* 插件的文档(https://github.com/jasonbellamy/jekyll-mermaid)有些问题，没有更上jekyll的更新,也是在这里踩坑了。

	* download mermaid.min.js and mermaid.min.js.map to /assets/js/vendor/
	* https://unpkg.com/mermaid@8.0.0/dist/mermaid.min.js
	* https://unpkg.com/mermaid@8.0.0/dist/mermaid.min.js.map
	* then edit the \_config.yml
	* bundle install
	* jekyll build
```
_config.yml 修改内容
plugins:
	...
	\- jekyll-mermaid
mermaid:
	src: /assets/js/vendor/mermaid.min.js
```

* 把mermaid集成到jekyll之中，不使用插件(github可用)

	* download mermaid.min.js and mermaid.min.js.map to /assets/js/vendor/
	* https://unpkg.com/mermaid@8.0.0/dist/mermaid.min.js
	* https://unpkg.com/mermaid@8.0.0/dist/mermaid.min.js.map
	* 在文档中引入mermaid.min.js (或者全局引入用)
	* &lt;script src="{{ site.url }}/assets/js/vendor/mermaid.min.js"&gt;&lt;/script&gt;

* 在md文档中，编写如下代码就可以实现mermaid流程图,previm也能正常显示

			<div class="mermaid" >
				graph TB
					A[text]
					....
			</div>
			或者用一个mermaid的liquid包裹mermaid的代码
	
* 如图

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

