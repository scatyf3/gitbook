# 演化式设计

计划驱动的开发过程：以瀑布模型为代表

迭代和演化式的开发过程：敏捷开发

两种设计方法的问题都与软件变化曲线相关，即随着软件项目的进展修改软件的成本呈指数级增长（即时间越长修改越困难）

Martin Fowler建议将持续集成、测试、重构相结合，实现“抚平变化曲线”的目标

持续集成使得不同开发人员的工作可以持续保持同步，不用担心个人所做的修改与其他人的代码出现集成问题\
测试可以及时暴露代码中的问题并提供反馈，同时为开发团队创造一种安全感\
持续和系统性的重构则可以不断将设计思考引入软件中

\->上述三个要点已经成为敏捷开发的重要部分

在此基础上，可以考虑将计划设计与演化式设计相结合，并找到一个合适的平衡点

## 代码坏味道

通过识别代码坏味道来识别重构机会

​![image](<.gitbook/assets/image 20231225010942 z8n1vz9.png>)​

在现实生活中：一些ide提供了一些自动化的重构操作，比如重命名变量
