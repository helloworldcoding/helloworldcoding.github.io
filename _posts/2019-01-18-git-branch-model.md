---
layout: post
title: "A git branch model"
mermaid: 1
---


<div class="mermaid">
	sequenceDiagram
		participant MasterG2
		participant Master
		participant Dev
		participant Test
		participant Release
		participant ReleaseG2

		Note over MasterG2,ReleaseG2:特性分支开发流程 
		Master ->> Dev:开发拉f分支
		Dev -->> Dev: 开发自测
		loop 提测
			Dev ->> Test:F分支合并到Test分支
			Note over Test:是否有冲突
			alt 有冲突
				Test ->> Dev :先解决冲突，再提测
			else 没有冲突
				Note over Test :jenkins构建test环境
				Note over Test:验证测试环境
				alt 测试环境验证通过
					Test ->> Release:F分支合并到Release
					Note over Release :是否有冲突
					alt 有冲突
						Release ->> Test : 先解决冲突
					else  没有冲突
						Note over Release :jenkins构建beta g0
						Note over Release:验证beta g0环境
					end
				else 不通过
					Test ->> Dev:测试环境不通过
				end
			end
		end
</div>



