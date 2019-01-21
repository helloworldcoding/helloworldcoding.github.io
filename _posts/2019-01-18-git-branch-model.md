---
layout: post
title: "A git branch model"
mermaid: 1
---


<div class="mermaid">
	sequenceDiagram
		participant MasterG2
		participant Master
		participant Dev as F分支
		participant Test
		participant Release
		participant ReleaseG2

		Note over MasterG2,ReleaseG2:特性分支(f分支)开发流程 
		Master ->>+ Dev:开发拉f分支
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
					Note over Dev :F分支能否合并到Release分支
					alt 能合并
						Dev ->> Release:F分支合并到Release
						Note over Release :是否有冲突
						alt 有冲突
							Release -->> Dev : 开发同学先解决冲突,再次提测
						else  没有冲突
							Note over Release :jenkins构建beta g0
							Note over Release:验证beta g0环境
							alt beta g0环境验证通过
								Note over Release : jenkins构建prod g0
								Note over Release : 验证prod g0
								alt prod g0验证通过
									Note over Release: 积累一天后
									Release ->>- Dev : 删除所有的合并到Relase的F分支
									Release ->> Master : 合并到Master
								else prod g0没有验证通过
									Release -->> Dev: prod g0验证没有通过，排查原因
								end
							else
								Release -->> Dev: beta g0测试不通过, 排查问题
							end
						end
					else F分支不能合并到Release,原因：
						Note over Dev: 过了14:00
						Note over Dev: 距离灰度发布不足两天
						Note over Dev: 灰度发布期间
					end
				else 测试环境验证不通过
					Test -->> Dev:测试环境不通过,排查问题
				end
			end
		end
</div>



