---
title: windows 11中更新后没有显示copilot图标解决方案
date: 2024-05-07 21:04:05
categories:
  - bug
---
> Windows 11中更新后没有显示copilot图标解决方案

1. 打开 C:\windows\system32\interateedServicesRegionPolizySet.json

2. 修改文件中最后一项json

   ````json
   {
   	"$comment":"show Copilot on taskbar by default when definitive answer is not received from Bing on user eligibility",
   	"guid":"{ff9e2d65-8af9-4235-a8c0-e4126475fb99}",
   	"defaultState":"enabled",
   	"conditions":{
   		"region": {"disabled": ["CN" "RU" , "BY", "IR", "CU", "KP", "SY"]
   			}
   		}
   	}
   ````

    - 将region中的CN去掉即可