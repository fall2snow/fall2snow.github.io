---
title: Perf工具使用  # 标题（自动生成，可修改）
date: 2025-08-12 15:30:00  # 发布时间（自动生成，可修改）
tags: [软件调试, 性能分析]  # 文章标签（可选，用逗号分隔）
categories: 软件调试  # 文章分类（可选，如 "技术"、"生活"）
description: erf工具使用  # 可选，用于SEO或摘要显示
---
# 1.板卡上运行->生成perf文件

-	perf record -e cpu-clock -g -p pid #得到perf.data文件
-	perf script -i perf.data > out.perf #得到out.perf文件
-	将out.perf传输到linux虚拟机/服务器

# 2.图像化

-	../FlameGraph/stackcollapse-perf.pl out.perf > out.floded #得到 out.floded 

-	../FlameGraph/flamegraph.pl out.floded > cp.svg #得到 cp.svg