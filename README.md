# 🎓 校园志愿服务全链路闭环运营与数字化管理系统
### Campus Volunteer Lifecycle Management & Engagement Platform (CV-System)

<p align="center">
  <img src="https://img.shields.io/badge/Spring%20Boot-3.2.1-brightgreen.svg?style=flat-square&logo=springboot" alt="Spring Boot" />
  <img src="https://img.shields.io/badge/Vue.js-3.4.21-4FC08D.svg?style=flat-square&logo=vuedotjs" alt="Vue 3" />
  <img src="https://img.shields.io/badge/TypeScript-5.4-blue.svg?style=flat-square&logo=typescript" alt="TypeScript" />
  <img src="https://img.shields.io/badge/MyBatis--Plus-3.5.5-orange.svg?style=flat-square" alt="MyBatis-Plus" />
  <img src="https://img.shields.io/badge/Redis-Cache%20%26%20Lock-red.svg?style=flat-square&logo=redis" alt="Redis" />
  <img src="https://img.shields.io/badge/MySQL-8.0+-4479A1.svg?style=flat-square&logo=mysql" alt="MySQL" />
  <img src="https://img.shields.io/badge/License-MIT-yellow.svg?style=flat-square" alt="License" />
</p>

---

## 📌 项目概述 (Executive Summary)

**Campus Volunteer System (CV-System)** 是一套面向现代高校志愿公益场景研发的**全生命周期闭环数字化管理平台**。

传统高校志愿管理多停留在“活动发布 - 简单报名 - 人工登记”的粗放模式，存在**签到易冒领、高难度活动缺乏前置培训考核、志愿者参与缺乏长期激励、多端操作体验割裂**等痛点。

本项目针对上述问题，通过 **Spring Boot 3 + Vue 3 + TypeScript** 架构，重构了高校志愿服务业务链路，打通了 **「岗位发布 ➔ 准入培训 ➔ 动态防伪签到 ➔ 工时认证 ➔ 积分商城激励 ➔ 内容社区留存 ➔ 全局审计治理」** 的完整闭环。

---

## 🏛️ 系统架构设计 (System Architecture)

```
┌────────────────────────────────────────────────────────────────────────┐
│                        前端展现层 (Presentation Layer)                 │
│   Vue 3.4 + TypeScript + Vite + Element Plus + Pinia + GSAP + Three.js │
├────────────────────────────────────────────────────────────────────────┤
│  [移动端 App Shell 交互体系]   │       [PC 端 SaaS 响应式工作台]       │
│  - 沉浸式导航 / 扫码核销组件   │       - 组织者管控大屏 / 数据可视化   │
│  - 抽奖大转盘 / 课程考核答题   │       - 管理端全局运营治理与风控审计   │
└────────────────────────────────────┬───────────────────────────────────┘
                                     │ RESTful API / JWT Bearer Token
┌────────────────────────────────────▼───────────────────────────────────┐
│                        后端业务驱动层 (Business Layer)                 │
│               Spring Boot 3.2.1 + Spring Security 6 + JJWT             │
├────────────────────────────────────────────────────────────────────────┤
│  [身份鉴权体系]    RBAC 三角色权限分立 / 动态安全上下文 / 会话自校验    │
│  [业务核心服务]    活动招募引擎 / 准入考核引擎 / 动态核销防伪签到服务  │
│  [成长激励中心]    签到流水打卡 / 积分资产账本 / 道具背包 / 幸运轮盘   │
│  [风控治理切面]    AOP 操作留痕审计 / DFA 算法社区敏感词实时过滤拦截  │
└────────────────────────────────────┬───────────────────────────────────┘
                                     │ ORM / Persistence & Cache
┌────────────────────────────────────▼───────────────────────────────────┐
│                        数据持久与缓存层 (Storage Layer)                │
├───────────────────────────────────┬────────────────────────────────────┤
│         MySQL 8.0+ (InnoDB)       │         Redis 6.0+ (In-Memory)     │
│   - 32+ 张高度范式化业务数据表    │   - 签到随机 Nonce / 防重锁控制    │
│   - SchemaInitializer 自动迁移    │   - 高频数据缓存 / 热点活动加速    │
└───────────────────────────────────┴────────────────────────────────────┘
```

---

## 💡 核心业务创新与工程亮点 (Key Innovations)

### 1. 动态安全防伪签到与闭环核销体系
* **痛点**：传统纸质签名或静态二维码极易出现截屏转发、代签、蹭工时问题。
* **架构方案**：
  * 组织者端现场生成携带 `activity_id`、`随机 nonce` 及 `有效期时间戳` 的动态加密二维码。
  * 志愿者端基于 `html5-qrcode` 唤起设备相机实时解析。
  * 服务端执行 **4重安全锁** 校验校验链：
    1. **活动生命周期锁**：校验当前活动是否处于允许签到状态区间；
    2. **志愿身份锁**：校验报名状态（仅限 `status = 1` 审核通过人员）；
    3. **时效防重放锁**：基于 Redis 校验 Nonce 有效期，避免截图二手转发；
    4. **幂等状态锁**：数据库唯一索引与原子操作保证无单人重复签到。

### 2. “学-考-准入”一体化培训考核流
* 针对大型赛事、医疗救助等高门槛志愿岗位，建立前置门槛考核流程。
* 支持多媒体课程学习进度追踪（视频断点续播、时长上报统计）。
* 题库随机抽取生成试卷，前端在线计时答题，后端自动评分并持久化考核凭证。未通关者直接限制核心活动报名。

### 3. DOM 级端分流体验 (One Codebase, Dual Experience)
* 区别于单纯的 CSS 媒体查询压缩页面，本项目在 `MainLayout.vue` 中实现了基于角色与运行环境的 **DOM 级逻辑分流渲染**：
  * **志愿者移动端**：原生 App 级视口体验，支持底部悬浮 Tabbar、页面转场过渡与微交互动效。
  * **管理端与桌面端**：沉浸式宽屏 SaaS 仪表盘，支持侧边栏折叠导航、多级权限菜单与 ECharts 复杂指标分析。

### 4. 积分经济学与可持续活跃激励
* 构建完整的用户成长与资产账户模型：
  * **获取途径**：每日签到（支持断签补签）、工时结算折算、心得发布与优质评论。
  * **消耗途径**：积分商城实体/虚拟奖品兑换（具有库存与订单履约管理）、幸运大转盘随机抽奖。
  * **资产背包**：支持卡券、道具背包独立存放，形成志愿服务正向激励生态。

### 5. 内容安全与运营风控审计
* **AOP 业务审计**：对组织者/管理员的所有敏感情报修改、工时变更、资格审批做无侵入式切面拦截，记录 IP、执行耗时与参数留痕。
* **敏感词实时拦截**：在志愿者心得交流广场集成 DFA 敏感词过滤检测，杜绝校园不良舆论发酵。

---

## 🧩 核心功能矩阵 (Feature Matrix)

| 业务板块 | 志愿者端 (Volunteer) | 组织者端 (Organizer) | 管理员端 (Administrator) |
| :--- | :--- | :--- | :--- |
| **活动全生命周期** | 检索分类、报名申请、实时取消、详情预览 | 发起筹备、名额配置、人员初筛录取 | 全局活动审核、上下线管控、异常下架 |
| **现场核销执行** | 摄像头扫码、动态核验、签到状态反馈 | 动态大屏投屏、人工补签、误签回滚 | 签到日志穿透核查、防刷异常追踪 |
| **工时与认证** | 工时流水查询、履职时长凭证生成 | 活动工时录入、评分反馈、结算下发 | 全校工时大盘监控、组织学分互认核销 |
| **准入培训学院** | 课程自学、计时考核、错题复习、资格解锁 | 录入专属培训课程、组卷与题库绑定 | 培训达标率分析、课程资源审核 |
| **资产与激励中心** | 签到领积分、积分兑换、背包道具、抽奖 | 申请组织专属兑换券额度 | 商品库上架、库存核销、中奖概率配置 |
| **社区与治理** | 经验心得发布、图文互动、点赞收藏 | 发布组织专属活动招募公告 | 违规心得下架、敏感词拦截、用户禁言 |
| **系统风控与配置** | 个人画像查看、密码修改、消息中心 | 组织资质认证更新、成员信息管理 | 角色权限编排、字典配置、AOP 审计日志 |

---

## 🛠️ 技术选型栈 (Tech Stack)

### 前端技术栈 (Frontend)
* **核心框架**：Vue 3.4 (`<script setup lang="ts">` 组合式 API)
* **类型系统**：TypeScript 5.4
* **工程构建**：Vite 5.1
* **UI 组件库**：Element Plus 2.13
* **全局状态**：Pinia 2.3
* **路由管理**：Vue Router 4.6
* **交互增强**：GSAP 3.14 (动效驱动) + Three.js (3D 可视化)
* **图表与设备能力**：ECharts 6.0 + html5-qrcode 2.3 + qrcode.vue

### 后端技术栈 (Backend)
* **主框架**：Spring Boot 3.2.1
* **安全鉴权**：Spring Security 6.x + JJWT 0.12.3 (无状态 Token 验证)
* **持久化**：MyBatis-Plus 3.5.5
* **数据库**：MySQL 8.0+
* **缓存加速**：Spring Data Redis (Lettuce 连接池)
* **API 文档规范**：Knife4j 4.4.0 (基于 OpenAPI 3)
* **通用工具集**：Hutool 5.8.25 + Lombok

---

## 📂 源码工程目录结构 (Project Layout)

```text
Campus-Volunteer-System/
├── backend/                               # Spring Boot 后端工程
│   ├── src/main/java/com/volunteer/
│   │   ├── aspect/                       # AOP 切面 (操作日志审计、敏感词拦截)
│   │   ├── common/                       # 全局通用返回体 (Result)、通用常量
│   │   ├── config/                       # 核心配置 (Security, Redis, Mybatis, SchemaInitializer)
│   │   ├── controller/                   # 业务控制层 (活动、签到、考试、商城、社区等)
│   │   ├── dto/                          # 数据传输对象 (入参验证模型)
│   │   ├── entity/                       # 数据库持久化实体模型 (32+ 表映射)
│   │   ├── exception/                    # 全局异常捕获器与自定义业务异常
│   │   ├── mapper/                       # MyBatis-Plus 数据访问接口
│   │   ├── scheduler/                    # 定时任务调度器 (活动过期结算等)
│   │   ├── security/                     # Spring Security 过滤器链与 Token 解析器
│   │   ├── service/                      # 业务逻辑服务接口及实现类
│   │   ├── util/                         # 加密、JWT、SensitiveFilter、树构建等工具类
│   │   └── vo/                           # 业务视图返回对象
│   └── src/main/resources/
│       ├── mapper/                       # MyBatis XML 映射文件
│       └── application.yml               # 主环境配置
├── frontend/                              # Vue 3 前端工程
│   ├── src/
│   │   ├── assets/                       # 静态资源与样式资产
│   │   ├── components/                   # 全局可复用组件 (吉祥物动效、扫码器等)
│   │   ├── layouts/                      # 多角色/双端分流渲染主框架 (MainLayout)
│   │   ├── router/                       # 路由表定义与权限路由守卫
│   │   ├── stores/                       # Pinia 状态树 (用户凭证、全局配置)
│   │   └── views/                        # 业务页面体系
│   │       ├── admin/                    # 管理员全功能控制台 (审核/监控/配置)
│   │       ├── organizer/                # 组织者工作台 (发活动/核销/工时录入)
│   │       ├── volunteer/                # 志愿者端 (移动端流式界面 + PC 工作区)
│   │       ├── training/                 # 培训学习与在线考试中心
│   │       ├── mall/                     # 积分商城与资产背包
│   │       └── experience/               # 志愿心得广场与图文互动
│   ├── package.json
│   └── vite.config.ts
└── README.md                              # 项目说明文档
```

---

## 🚀 快速开始与环境搭建 (Quick Start)

### 1. 环境准备 (Prerequisites)
| 依赖项 | 推荐版本 | 说明 |
| :--- | :--- | :--- |
| **JDK** | 17 LTS 或更高 | Spring Boot 3 必需基线 |
| **Node.js** | 18.x / 20.x LTS | 前端编译与运行环境 |
| **MySQL** | 8.0+ | 核心关系型数据库 |
| **Redis** | 6.0+ | 缓存及分布式防重锁 |
| **Maven** | 3.8+ | 后端依赖管理与构建工具 |

---

### 2. 数据库配置与启动 (Database)

1. 进入 MySQL 客户端，创建空数据库：
   ```sql
   CREATE DATABASE volunteer_system DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
   ```
2. **零初始导入成本**：本项目内置了 `SchemaInitializer` 自动化检测工具，后端首次启动时会自动同步并修复核心表结构与初始状态。

---

### 3. 后端服务启动 (Backend Setup)

1. 打开 `backend/src/main/resources/application.yml`，核对数据库与 Redis 凭证：
   ```yaml
   spring:
     datasource:
       url: jdbc:mysql://localhost:3306/volunteer_system?useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=Asia/Shanghai
       username: root
       password: 你的数据库密码
     data:
       redis:
         host: localhost
         port: 6379
   ```
2. 使用 Maven 构建并运行：
   ```bash
   cd backend
   mvn clean spring-boot:run
   ```
3. 服务就绪标识：
   * 业务接口基地址：`http://localhost:8080/api`
   * Knife4j API 文档入口：`http://localhost:8080/api/doc.html` (或 `swagger-ui.html`)

---

### 4. 前端应用启动 (Frontend Setup)

1. 进入前端目录并安装依赖包：
   ```bash
   cd frontend
   npm install
   ```
2. 启动本地开发服务：
   ```bash
   npm run dev
   ```
3. 访问控制台输出的地址即可进入系统：`http://localhost:5173`。
   > **调试建议**：在浏览器按下 `F12` 并切换为“移动设备模拟”视图，可沉浸式体验志愿者端的 App 交互流！

---

## 🔒 演示账号体系 (Demo Accounts)

系统预设了多套分权演示角色供测试体验：

| 角色类型 | 用户名 | 初始密码 | 体验功能重点 |
| :--- | :--- | :--- | :--- |
| **系统超级管理员** | `admin` | `123456` | 全局仪表盘、活动资格审核、内容审计与敏感词风控 |
| **组织者代表** | `organizer` | `123456` | 活动发布、动态签到码核销、工时结算审核 |
| **注册志愿者** | `volunteer` | `123456` | 移动端活动报名、扫码模拟、课程考试、积分商城 |

---

## 📄 开源许可协议 (License)

本项目采用 [MIT License](LICENSE) 协议开源。欢迎高校师生、开发者在此基础上二次开发或用于学术研究。
