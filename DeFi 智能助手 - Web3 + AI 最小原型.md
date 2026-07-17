# 🚀 DeFi 智能助手 - Web3 + AI 最小原型

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Web3](https://img.shields.io/badge/Web3-ethers.js-blue)](https://docs.ethers.org/)
[![AI](https://img.shields.io/badge/AI-DeepSeek-orange)](https://platform.deepseek.com/)

> 一句话描述：**自然语言 → 交易预览**，一个结合 Web3 和 AI 的最小原型演示工具。

---

## 📖 项目简介

**DeFi 智能助手** 是一个 Web3 + AI 最小原型项目。

用户用自然语言输入交易意图（如"买 0.5 个以太"），系统自动：
1. **AI 解析**：提取动作、币种、数量
2. **实时价格**：从 CoinGecko 获取真实价格
3. **链上预览**：连接钱包后生成交易数据预览

**核心价值**：让不懂区块链技术的用户，用自然语言就能理解交易逻辑。

---

## ✨ 功能特性

| 功能 | 描述 |
|------|------|
| 🔗 **连接钱包** | 支持 MetaMask，自动切换到 Sepolia 测试网 |
| 💰 **余额查询** | 显示 ETH 和 USDC 真实链上余额 |
| 🤖 **AI 解析** | 使用 DeepSeek API 理解自然语言 |
| 💵 **实时价格** | CoinGecko 免费 API 获取 ETH/BTC 价格 |
| 📡 **交易预览** | 生成模拟交易数据（to, value, from） |
| 🎨 **美观界面** | 响应式设计，支持移动端 |

---

## 🛠️ 技术栈

| 类别 | 技术 |
|------|------|
| 前端 | HTML5 + CSS3 + JavaScript |
| Web3 | ethers.js + MetaMask |
| AI | DeepSeek API |
| 价格数据 | CoinGecko API |
| 区块链网络 | Sepolia 测试网 |

---

## 🎯 项目定义（按提交要求）

### 我要做的最小功能是什么？

**一个"自然语言 → 交易预览"的 Web3 + AI 演示工具**

用户输入中文交易意图（如"买 0.5 个以太"），系统通过 AI 解析意图，结合实时价格和链上数据，展示交易预览。

---

### 谁会使用它？

| 用户类型 | 使用场景 |
|---------|----------|
| **初级 DeFi 用户** | 不理解合约 ABI 和交易参数，想通过自然语言了解交易成本 |
| **产品演示者** | 向客户或团队展示 Web3 + AI 结合的可能性 |
| **开发者学习者** | 学习如何调用 AI API、读取链上数据、连接钱包 |

---

### 用户完成的一个动作是什么？

**完整流程**：

1. 用户打开网页
2. （可选）点击"连接钱包"
3. 输入："买 0.5 个以太"
4. 点击"智能解析"
5. 系统返回：
   - ✅ 动作：买入
   - 💰 数量：0.5 ETH
   - 💵 单价：~$3200 USDC（实时）
   - 🧮 总价：~$1600 USDC
   - 📡 链上交易预览（如果连接了钱包）

---

### 我需要读哪 1–3 个文档？

| 序号 | 文档 | 用途 | 重要程度 |
|------|------|------|----------|
| 1 | [DeepSeek API 文档](https://platform.deepseek.com/api-docs/) | 调用 AI 解析自然语言 | ⭐⭐⭐ |
| 2 | [ethers.js 文档](https://docs.ethers.org/) | 连接钱包、查询余额、构造交易 | ⭐⭐⭐ |
| 3 | [CoinGecko API 文档](https://www.coingecko.com/en/api) | 获取实时加密货币价格 | ⭐⭐ |

**实际学习方式**：通过官方快速示例 + ChatGPT 辅助，没有完整通读文档。

---

### 本周真实实现什么？哪些可以 mock？

#### ✅ 真实实现

| 模块 | 实现方式 |
|------|----------|
| 前端界面 | HTML + CSS，紫色渐变卡片设计 |
| AI 解析 | DeepSeek API（function calling 风格） |
| 实时价格 | CoinGecko 免费 API（无需 Key） |
| 钱包连接 | MetaMask + ethers.js |
| 余额查询 | ETH 原生余额 + USDC ERC-20 余额 |
| 交易预览 | 构造模拟交易数据（to, value, from） |

#### 🟡 Mock（后续可扩展）

| 项目 | Mock 方式 | 后续扩展 |
|------|----------|----------|
| 真实上链交易 | 只展示交易数据，不发送 `sendTransaction` | ✅ 可添加 |
| 多链支持 | 只支持 Sepolia 测试网 | ✅ 可添加主网/其他测试网 |
| 复杂路由 | 只做单币兑换预览 | ✅ 可接入 Uniswap 路由 |
| 用户历史记录 | 不保存，每次刷新重置 | ✅ 可接入数据库 |
| 价格影响模拟 | 用固定公式 `amount * price` | ✅ 可接入 DEX 报价合约 |

---

### 我如何证明它做出来了？

#### 交付物清单

- [x] 可运行的 HTML 文件（`index.html`）
- [x] 3 个测试用例截图
- [x] 完整的 README 文档
- [x] 学习复盘笔记

#### 3 个测试用例

| 输入 | 预期输出 | 实际结果 |
|------|---------|----------|
| `买 0.5 个以太` | 显示 0.5 ETH ≈ $1600 USDC | ✅ 通过 |
| `卖 2 个比特币` | 显示 2 BTC ≈ $120000 USDC | ✅ 通过 |
| `换 100 USDC` | 显示 100 USDC ≈ $100 USDC | ✅ 通过 |

#### 截图示例

<img width="1440" height="900" alt="截屏2026-07-17 15 20 51" src="https://github.com/user-attachments/assets/57b63b63-85ab-4eb8-a535-1af83e5ca17a" />
/>
