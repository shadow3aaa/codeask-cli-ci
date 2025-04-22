### 项目结构分析报告

#### 一、项目架构概览

**1. 主要模块和组件**

├── 配置模块
├── AI服务集成模块
├── 代码分析引擎
├── 文件处理模块
├── 模板引擎模块
└── CLI交互模块


**2. 目录结构树形图**

codeask-cli/
├── config/
│   └── codeask.yaml       # 核心配置文件
├── src/
│   ├── cli.py             # CLI命令入口
│   ├── analyzer/          # 代码分析引擎
│   │   ├── core.py        # 并发分析控制器
│   │   └── parser.py      # AI响应解析器
│   ├── services/          # AI服务集成
│   │   ├── openai.py
│   │   ├── anthropic.py
│   │   └── factory.py     # 服务工厂
│   ├── utils/
│   │   ├── filters.py     # 文件过滤处理器
│   │   └── templates.py   # 提示词模板引擎
└── outputs/               # 分析报告输出目录


**3. 架构设计模式**
采用 **分层架构** 与 **工厂模式** 组合设计：
- **控制层**：CLI命令解析与执行流控制
- **服务层**：AI服务工厂动态创建不同供应商实现
- **数据层**：配置文件与模板的读取解析
- **适配器模式**：统一不同AI服务的API调用接口

**4. 分层架构图（Mermaid）**
mermaid
graph TD
    A[CLI交互层] --> B[分析控制层]
    B --> C[AI服务层]
    C --> D[数据处理层]
    D --> E[配置/模板]
    style E fill:#f9f,stroke:#333


#### 二、模块依赖分析

**1. 模块依赖图（Mermaid）**
mermaid
graph LR
    CLI[CLI模块] --> ConfigLoader[配置加载器]
    CLI --> Analyzer[分析引擎]
    Analyzer -->|依赖| AIFactory[AI服务工厂]
    Analyzer --> FileFilter[文件过滤器]
    Analyzer --> TemplateEngine[模板引擎]
    AIFactory --> OpenAIService[OpenAI实现]
    AIFactory --> AnthropicService[Anthropic实现]
    TemplateEngine -->|读取| YamlTemplates[YAML模板]


**2. 关键模块职责说明**

| 模块名称         | 职责描述                                                                 |
|------------------|------------------------------------------------------------------------|
| **配置加载器**   | 解析YAML配置，验证API密钥，处理环境变量注入                              |
| **AI服务工厂**   | 根据provider配置动态创建对应AI服务实例，统一接口返回                      |
| **分析引擎**     | 控制并发文件处理流程，协调过滤器与模板引擎，处理AI响应解析                |
| **文件过滤器**   | 应用glob规则过滤目标文件，支持排除模式处理                               |
| **模板引擎**     | 动态注入代码内容到预设模板，生成符合AI要求的提示词                        |
| **响应解析器**   | 标准化AI返回结果，提取结构化数据，处理可能的异常响应格式                  |

#### 三、核心流程说明
mermaid
sequenceDiagram
    participant User as 用户
    participant CLI as CLI工具
    participant Analyzer as 分析引擎
    participant AI as AI服务
    
    User->>CLI: 执行分析命令
    CLI->>ConfigLoader: 加载配置
    ConfigLoader-->>CLI: 返回配置对象
    CLI->>Analyzer: 初始化分析器
    Analyzer->>FileFilter: 获取目标文件列表
    FileFilter-->>Analyzer: 返回过滤后文件
    loop 并发处理文件
        Analyzer->>TemplateEngine: 生成提示词
        TemplateEngine-->>Analyzer: 返回完整prompt
        Analyzer->>AI: 发送分析请求
        AI-->>Analyzer: 返回原始结果
        Analyzer->>Parser: 解析结构化数据
    end
    Analyzer->>CLI: 生成最终报告
    CLI->>User: 输出分析结果


#### 四、扩展能力设计
1. **插件化扩展**：通过实现`BaseAIService`接口可快速新增AI服务支持
2. **模板热更新**：修改YAML模板文件无需重新编译即可生效
3. **动态过滤器**：支持用户自定义glob模式覆盖默认过滤规则

该架构通过清晰的层次划分和模块化设计，实现了代码分析工具的高扩展性和可维护性，各模块通过标准接口交互，符合单一职责原则。