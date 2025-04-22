# CodeAsk CLI Action

这是一个可复用的GitHub Action，可用于在工作流中执行代码检查任务。该Action使用codeask-cli工具自动生成代码说明文档。

## 功能介绍

CodeAsk CLI Action可以帮助你在CI/CD流程中自动运行codeask-cli工具，为你的代码生成详细的说明文档。它支持多种AI模型API，包括OpenAI、Anthropic Claude、Azure OpenAI和Google Gemini。

## 用法

在你的GitHub工作流中添加以下配置:

```yaml
jobs:
  generate-docs:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Generate code documentation
        uses: your-username/codeask-cli-ci@v1
        with:
          openai_api_key: ${{ secrets.OPENAI_API_KEY }}
          # 其他可选参数...
```

## 输入参数

| 参数名 | 描述 | 是否必需 | 默认值 |
|-------|------|---------|-------|
| working-directory | 执行codeask-cli的工作目录 | 否 | . |
| openai_api_key | OpenAI API key | 否 | |
| anthropic_api_key | Anthropic Claude API key | 否 | |
| azure_openai_api_key | Azure OpenAI API key | 否 | |
| gemini_api_key | Google Gemini API key | 否 | |

## API密钥说明

你至少需要提供以下API密钥中的一个才能使用此Action:

- `openai_api_key`: OpenAI API密钥
- `anthropic_api_key`: Anthropic Claude API密钥
- `azure_openai_api_key`: Azure OpenAI API密钥
- `gemini_api_key`: Google Gemini API密钥

建议将API密钥存储在GitHub仓库的Secrets中，以确保安全。

## 示例

### 基本用法

```yaml
name: Generate Code Documentation

on:
  push:
    branches: [ main ]

jobs:
  docs:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Generate code documentation
        uses: your-username/codeask-cli-ci@v1
        with:
          openai_api_key: ${{ secrets.OPENAI_API_KEY }}
```

### 使用自定义工作目录

```yaml
- name: Generate code documentation
  uses: shadow3aaa/codeask-cli-ci@v1
  with:
    working-directory: './src'
    openai_api_key: ${{ secrets.OPENAI_API_KEY }}
```
