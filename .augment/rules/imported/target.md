---
type: "agent_requested"
description: "Example description"
---
# target.md
## 项目背景
1. 背景描述
1.1 现状描述
BI数据平台目前提供自定义维度分组功能，用户可以将维度值（如渠道、地区等）进行手动分组，便于汇总统计收入、新增等关键指标。
1.2 核心痛点

效率瓶颈：每当出现新的维度值，用户必须手动将其添加到对应分组
频繁更新：维度值（特别是渠道）更新频繁，人工维护成本高
规模挑战：维度值数量庞大，手动分组难以应对

1.3 典型场景
投放团队面临的实际问题：

渠道名称如"今日头条(效果)"、"头条直播(效果)"、"头条原生(效果)"需要归为一组
希望设置类似头条*(效果)的规则，自动捕获所有符合模式的渠道
当出现新的头条系渠道时，能自动归类，无需人工干预

2. 产品目标
2.1 核心目标
通过规则引擎实现维度值的自动分组，大幅提升分组效率，减少人工维护成本。
2.2 预期效果

新增维度值自动归类，减少90%以上的手动操作
支持灵活的规则配置，满足不同业务场景需求
保持现有手动分组能力，实现平滑过渡
规则逻辑透明可控，便于调试和维护

## 指导原则

- 1.是一个用于和开发团队沟通和演示的高保真demo
- 2.业务数据需要存储在 localstorage 里
- 3.不要在在一个大文件里塞过多的内容
- 4.项目需要是一个纯 web 的项目，且测试环境部署后方便局域网内访问，访问地址是我本机的ip+端口号的方式
- 5.所有功能需要能在前端真实演示，不要模拟效果，要真实功能
- 6.现有页面参考 html 文件 index.html,需要在这个页面基础上实现功能，并保持页面主题
- 7.预期中的项目目录结构：
src/
├── cli.tsx           # Main CLI entry point
├── components/       # React/Ink UI components
│   ├── App.tsx      # Main application component
│   ├── MessageHistory.tsx
│   ├── InputBox.tsx
│   └── StatusLine.tsx
├── services/        # Core business logic
│   ├── translator.ts
│   └── ai-client.ts
├── utils/           # Utility functions
└── types/           # TypeScript type definitions
- 8.一旦 project is set up，use these commands：
npm run dev      # Run the CLI in development mode using tsx
npm run build    # Build production CLI  
npm start        # Run the built CLI
npx tsx src/cli.tsx  # Direct TypeScript execution
- 9.关于产品的需求文档参照文件 prd.md中的描述