---
layout: post
title: "The new  git branch model(add G1)"
mermaid: 1
---


<div class="mermaid">
	sequenceDiagram
		participant MasterG2
		participant Master
		participant Dev as F分支
		participant Bg0
		participant Bg2 as Bg2/F2
		participant Test
		participant Release
		participant ReleaseG2

		Note over Master,Release:特性分支(f分支)开发流程 
		Master ->>+ Dev:开发拉f分支
		Dev -->> Dev: 开发自测
		loop 提测
			Dev ->> Test:F分支合并到Test分支
			Note over Test:是否有冲突
			alt 没有有冲突
				Note over Test :jenkins构建test环境
				Note over Test:验证测试环境
				alt 测试环境验证通过
					Note over Dev :F分支能否合并到Release分支
					alt 能合并
						Dev ->> Release:F分支合并到Release
						Note over Release :是否有冲突
						alt 没有冲突
							Note over Release :jenkins构建beta g0
							Note over Release:验证beta g0环境
							alt beta g0环境验证通过
								Note over Release: 积累一天
								Note over Release : jenkins构建prod g0
								Note over Release : 验证prod g0
								alt prod g0验证通过
									Release ->>- Dev : 删除所有的合并到Relase的F分支
									Release ->> Master : Release分支合并到Master
								else prod g0没有验证通过
									Release -->> Dev: prod g0验证没有通过，排查原因
								end
							else
								Release -->> Dev: beta g0测试不通过, 排查问题
							end
						else  有冲突
							Release -->> Dev : 开发同学先解决冲突,再次提测
						end
					else F分支不能合并到Release,原因：
						Note over Dev: 过了14:00
						Note over Dev: 距离灰度发布不足两天
						Note over Dev: 灰度发布期间
					end
				else 测试环境验证不通过
					Test -->> Dev:测试环境不通过,排查问题
				end
			else 有冲突
				Test -->> Dev :先解决冲突，再提测
			end
		end

		Note over Master,Release:Bg0分支开发流程(解决线上g0环境的bug)
		Note over Release: 灰度前后验证prod G0环境
		alt prod g0环境验证通过
		else prod g0验证不通过
			Master ->>+ Bg0 : 从Master拉取一个Bg0分支
			Bg0 -->> Bg0 : 开发自测
			Bg0 ->> Test : 合并到Test分支
			alt Bg0合并到Test没有冲突
				Note over Test:	jenkins 构建测试环境，并验证
				alt 测试环境验证通过
					Bg0 ->> Release: Bg0合并到Release分支 
					alt 合并没有冲突
						Note over Release :jenkins构建beta g0环境，并验证
						alt beta g0验证通过
							Note over Release:构建 prod g0,并验证
							alt prod g0通过
								Release ->> Master: 合并到Master分支,删除Bg0分支
								deactivate Bg0
							else  失败
								Release -->> Bg0 : prod g0未通过，排查原因，再提测
							end
						else 失败
							Release -->> Bg0: beta g0未通过，排查原因，再提测	
						end
					else Bg0合并Release发生冲突
						Release -->> Bg0: 解决冲突，重新提测
					end
				else 测试环境验证不通过
					Test -->> Bg0: 查找原因，重新提测
				end
			else Bg0合并到Test有冲突 
				Test -->> Bg0:解决冲突，重新提测	
			end
		end

		Note over MasterG2, ReleaseG2 : 全量发布
		Note right of ReleaseG2:prod环境全量发布
		Note over Release,ReleaseG2 :把prod g2的租户漂移到prod g0环境
		Release ->> ReleaseG2: Release分支合并到RelaseG2
		Note over ReleaseG2:jenkins构建prod g2环境
		Note over Release,ReleaseG2 :把大部分prod g0租户漂移回prod g2环境

		Note right of ReleaseG2:beta环境全量发布
		Note over Release,ReleaseG2 :把beta g2的租户漂移到beta g0环境
		Note over ReleaseG2:jenkins构建beta g2环境
		Note over Release,ReleaseG2 :把大部分beta g0租户漂移回beta g2环境

		ReleaseG2 ->> MasterG2 : 全量发布完成，ReleaseG2合并到MasterG2



		Note over MasterG2,ReleaseG2:Bg2/F2分支开发流程(解决线上g2环境的bug(Bg2分支),或G2环境的需求(F2分支))
		MasterG2 ->>+ Bg2: 拉取Bg2/F2分支	
		Bg2 -->> Bg2: 开发自测
		Bg2 ->> Test: 合并到test分支
		Note over Test:jenkins构建测试环境，并验证
		alt 测试环境验证通过
			Bg2 ->> ReleaseG2 : 合并到ReleaseG2分支
			alt 合并成功，没有冲突
				Note over ReleaseG2:jenkins构建beta g2环境
				Note over ReleaseG2:在beta g2环境验证
				alt beta g2环境验证通过
					Note over ReleaseG2: jenkins构建prod g1环境
					Note right of  ReleaseG2: 从G2选取部分租户<br/>漂移到prod g1
					alt prod g1租户验证不通过
						ReleaseG2 -->> Bg2:验证不通过，查原因，重新提测
					else prod g1的租户验证通过
						Note over ReleaseG2: 在prod g1等待
						ReleaseG2 ->> Release : 合并到Release
						ReleaseG2 ->> MasterG2: 合并到MasterG2<br/>并删除Bg2分支
						deactivate Bg2
					end
					Note right of  ReleaseG2: 租户在prod g1上<br/>过了观察期(如1天)
					Note right of  ReleaseG2: 把租户全部漂移<br/>到prod g1
					Note over ReleaseG2: jenkins构建prod g2环境
					Note right of  ReleaseG2: 把prod g1租户<br/>全部漂移到prod g2
				else beta g2环境验证不通过
					ReleaseG2 -->> Bg2: beta g2验证不通过，查原因，重新提测
				end
			else Bg2/F2合并ReleaseG2,发生冲突 
				ReleaseG2 -->> Bg2:解决冲突，重新接测	
			end
		else
			Test -->> Bg2:测试环境不通过，查原因，重新提测
		end
</div>





