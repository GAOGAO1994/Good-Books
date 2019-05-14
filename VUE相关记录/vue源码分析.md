[TOC]

# vue源码分析

## 1 配置文件的要点

1. config/index.js

   52：productionSourceMap: true,  // 打包时，减小整个项目的体积

   // 加快打包速度，增大打包体积，依赖.map文件

2. http-server

   - 下载：npm 

   cmd：hs -o -p 9999      (9999:指定的端口)