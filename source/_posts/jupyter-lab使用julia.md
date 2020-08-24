---
title: jupyter lab使用julia
date: 2020-08-24 09:34:30
categories: 
tags: mac
mathjax: true
---
要想在jupyter lab/notebook中使用julia需要安装IJulia，但是如果不设置好，julia会单独设置自己的conda环境，占用很大的容量。思路是让julia和python使用同一个conda环境。

<!--more-->

1. 首先确保.julia/compile/{version}和.julia/packages中没有Conda和IJulia文件夹，如有需要删除.julia/Conda文件夹。
2. 在julia repl中设置ENV["CONDA_JL_HOME"] = "{anaconda3 path}"
3. 正常安装IJulia
```bash
add IJulia
using IJulia
```
4. 查看jupyter的调用路径
```bash
IJulia.JUPYTER
```
5. 在julia repl中输入notebook()可以直接打开jupyter notebook，或是在新terminal中打开jupyter lab
---

如果更新了julia版本，希望在jupyter lab中保持kernel选项干净整洁，可以删除旧版本的julia kernel
1. 查看所有已经安装的jupyter notebook 的 kernel
```bash
jupyter kernelspec list
```
2. 删除相应的kernel
```bash
jupyter kernelspec remove {kernel_name}
```