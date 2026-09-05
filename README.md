# 简仓 StockLite

可直接商用的 **Node.js 单体库存系统**：入库、出库、销售开单、角色权限、用户注册审核、库存告警、Excel 导出。无需单独安装数据库，一条命令即可运行。

- [产品说明](./docs/产品说明.md) — 给店主和仓管的功能手册
- [部署文档](./docs/部署文档.md) — 安装、开机自启、备份、Nginx、排错
- [产品宣传简介](./docs/产品宣传简介.md) — 中英文对外简介
- [产品报价单](./docs/产品报价单.md) — SaaS / 买断指导价
- [Gitee README 文案](./docs/Gitee-README.md) — 开源仓库介绍（可直接贴）

界面走「纸仓台账」风格，侧栏深林绿、内容暖纸色，适合仓库、门店、小型工厂的日常记账。

## 为什么这样设计

参考了 GitHub 上几类成熟库存项目的做法，但刻意做轻：

| 借鉴 | 取了什么 | 没有照搬什么 |
| --- | --- | --- |
| [Lite IMS](https://github.com/mrmeaow/lite-ims) | 角色权限、商品主数据、库存流水 | PERN 拆分、Redis、pnpm monorepo |
| [Invora](https://github.com/Tapetal/Inventory-Management-System) | JWT、Admin/Staff 分权、Excel 报表 | MongoDB、前后端分离部署 |
| [omni-stock](https://github.com/nezu511/omni-stock) | SQLite 单体、低库存告警 | 实验室试剂流程 |
| [Material Management System](https://github.com/st4rboy1/Material-Management-System) | 工作台指标、Helmet / 限流、xlsx 导出 | Prisma + PostgreSQL 重依赖 |

本项目选择 **Express + SQLite + 无构建前端**：易学、易备份、易交付。MIT 许可，可商用。

## 功能

- **注册 / 登录**：密码 bcrypt 加密，JWT HttpOnly Cookie；注册默认待审核
- **权限**：管理员、仓库主管、仓管员
  - 管理员：用户审核与角色、商品、入出库、销售、导出
  - 主管：商品维护、入出库、销售、导出（不能管用户）
  - 仓管：入出库、销售、查看库存与告警（不能改档案、不能导出）
- **入库 / 出库**：事务写库存，生成 `IN-` / `OUT-` 单号，库存不足禁止出库
- **销售开单**：购物车多商品，生成 `SL-` 销售单并同步扣库存
- **库存告警**：当前库存 ≤ 安全库存，工作台和告警页高亮，可导出补货清单
- **Excel 导出**：商品库存、库存流水、销售记录、告警（ExcelJS，带表头样式）

## 快速开始

需要 Node.js 22+（使用内置 SQLite，无需编译原生模块）。

```bash
cd simple-inventory
copy .env.example .env
npm install
npm run build
npm start
```

打开 http://localhost:3000

演示账号（免费体验默认 **2 账号 / 200 SKU**，上线前请立刻改密）：

| 角色 | 用户名 | 密码 |
| --- | --- | --- |
| 管理员 | `admin` | `Admin@123` |
| 仓管员 | `staff` | `Staff@123` |

开发热重载：

```bash
npm run dev
```

## 生产建议

1. 把 `.env` 里的 `JWT_SECRET` 换成足够长的随机串
2. 设置 `NODE_ENV=production`
3. 登录后修改默认账号密码，或停用演示账号
4. 数据文件在 `data/inventory.db`，备份只需拷贝该文件（及 `-wal` / `-shm`）
5. 用 pm2 / nssm / systemd 守护进程，前面可挂 Nginx

## 目录

```
src/                 后端（Express）
  routes/            登录、用户、商品、库存、销售、看板、导出
  services/          库存事务、Excel 生成
  middleware/        JWT 与权限
public/              前端（无构建，打开即用）
data/inventory.db    SQLite 数据（首次启动自动创建并写入演示数据）
```

## 技术栈

- 运行时：Node.js + Express
- 数据：Node.js 内置 SQLite（`node:sqlite`，WAL）
- 安全：helmet、express-rate-limit、zod 校验、bcryptjs、JWT
- 导出：exceljs
- 前端：原生 HTML / CSS / JS，零打包

## 许可证

MIT。可用于商业项目；软件按原样提供，请自行做好备份与权限管理。
