# 快速开始1 - 基于MetaBCI编写离线实验范式

MetaBCI是天津大学神经工程团队开发的一款基于Python的脑机接口实验设计框架。通过使用框架中的三个模块：Brainda、Brainstim以及Brainflow，研究人员可以快速地搭建起离线分析、刺激呈现以及在线分析模块。MetaBCI的还提供了多个demo，用于演示各功能模块的使用（[MetaBCI/demos](https://github.com/TBC-TJU/MetaBCI/tree/master/demos)）。但是，由于缺少文字引导，初次接触MetaBCI的研究人员需要从代码中推测实验的开发流程，加大了学习的成本。同时，笔者在使用MetaBCI的过程中遇到了许许多多的坑。为了方便后人快速上手，避免重复蹚雷，本文应运而生。本文基于一个简单的运动想象和SSVEP的混合BCI，演示如何使用MetaBCI快速编写一个实验范式。

## 实验范式的元素

设计一款实验范式，我们首先需要思考实现这个范式需要用到哪些元素（即实验过程中电脑屏幕中显示的内容）。我们希望设计一个基于运动想象和SSVEP的混合BCI实验范式，会用到哪些元素呢？对于运动想象而言，我们只需要两张图片，分别代表左手和右手，如下所示。

![left_hand](https://cdn.jsdelivr.net/gh/smart-huang/bolgImage@main/img/left_hand.png)

![right_hand](https://cdn.jsdelivr.net/gh/smart-huang/bolgImage@main/img/right_hand.png)