### 项目结构分析报告

#### 1. 项目架构概览

**主要模块和组件**
1. **配置管理模块**：处理YAML配置解析（codeask.yml）
2. **AI服务模块**：对接OpenAI/Anthropic等AI服务
3. **分析引擎模块**：实现并发代码分析
4. **报告生成模块**：根据模板生成结构化报告
5. **CI/CD模块**：自动化文档生成流程（action.yml）

**目录结构树形图**
bash
.
├── .github/
│   └── workflows/
│       └── action.yml         # CI/CD流程定义
├── configs/
│   └── codeask.yml           # 核心配置文件
├── src/
│   ├── cli/                  # CLI主程序
│   ├── analyzer/             # 代码分析引擎
│   ├── ai_service/           # AI服务适配器
│   └── templates/            # 报告模板库
└── docs/
    └── reports/              # 生成的报告输出目录


**架构设计模式**
采用 **分层架构** + **管道过滤器模式**：
- **配置层**：统一管理运行时参数
- **处理层**：并发执行代码分析
- **服务层**：AI服务抽象接口
- **输出层**：模板化报告生成

**分层关系图（Mermaid）**
mermaid
graph TD
    A[配置层] --> B[处理层]
    B --> C[服务层]
    B --> D[输出层]
    E[CI/CD层] -->|触发| B
    style A fill:#f9f,stroke:#333
    style B fill:#bbf,stroke:#333
    style C fill:#9f9,stroke:#333
    style D fill:#f99,stroke:#333
    style E fill:#ff9,stroke:#333


#### 2. 模块依赖分析

**模块依赖图（Mermaid）**
mermaid
graph TD
    Config[配置模块] --> Analyzer[分析引擎]
    Analyzer -->|API调用| AI[AI服务模块]
    Analyzer -->|模板填充| Report[报告生成器]
    CI[CI/CD模块] -->|调用| CLI[CLI入口]
    CLI -->|配置加载| Config
    CLI -->|启动| Analyzer
    Analyzer -->|并发控制| Pool[线程池]
    Pool -->|文件处理| Worker[分析工作单元]
    Worker -->|数据转换| Parser[YAML解析器]


**关键模块职责说明**

| 模块名称       | 职责描述                                                                 |
|----------------|--------------------------------------------------------------------------|
| **配置模块**   | 解析YAML配置，验证API密钥有效性，提供运行时参数                         |
| **分析引擎**   | 管理线程池，协调文件扫描与分析的并发执行                                 |
| **AI服务模块** | 封装不同AI供应商的API调用，实现统一的提示词构建和响应解析接口            |
| **报告生成器** | 根据模板将分析结果转换为Markdown/HTML格式，支持自定义CSS样式             |
| **CI/CD模块**  | 自动化环境配置（Python/pipx），安全注入密钥，触发文档生成任务            |

**核心数据流**
mermaid
sequenceDiagram
    participant CI as CI/CD模块
    participant Config as 配置模块
    participant Analyzer as 分析引擎
    participant AI as AI服务
    
    CI->>Config: 加载codeask.yml
    Config-->>CI: 返回配置对象
    CI->>Analyzer: 初始化分析引擎
    Analyzer->>AI: 建立API连接
    loop 文件分析
        Analyzer->>Analyzer: 并发处理文件
        Analyzer->>AI: 发送分析请求
        AI-->>Analyzer: 返回结构化分析
    end
    Analyzer->>Report: 生成最终报告
