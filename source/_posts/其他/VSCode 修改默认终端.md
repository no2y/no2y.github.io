---
title: VSCode 修改默认终端
date: 2023-12-18 11:58:53
updated: 2023-12-18 11:58:53
tags:
  - vscode
categories:
  - 随记
---
`设置` `右上角` `打开设置(json)`
添加以下内容，路径请替换为实际的
```json
{
// ... 其他设置
"terminal.integrated.profiles.windows": {
	"Git-Bash": {
	  "path": "E:\\Git\\bin\\bash.exe"
	}
},
 "terminal.integrated.defaultProfile.windows": "Git-Bash"
}
```
之后 (Windows) 使用 `Ctrl` + `Shift` + `P` 输入 `Reload Window` 重载 VSCode 
再次打开终端，可以发现已经是 `git bash` 的模样了