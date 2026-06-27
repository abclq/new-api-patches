# new-api 渠道健康路由补丁

[![Go](https://img.shields.io/badge/Go-1.22+-00ADD8)](https://go.dev)
[![Upstream](https://img.shields.io/badge/upstream-CalciumIon%2Fnew--api-green)](https://github.com/Calcium-Ion/new-api)

配合 [Calcium-Ion/new-api](https://github.com/Calcium-Ion/new-api)（AI API 网关）使用的增强补丁，为渠道选择增加健康感知路由。

## 🎯 解决了什么问题

原版 new-api 在多渠道负载均衡时，会**无差别轮询所有渠道**——包括刚刚返回 502/超时的渠道。这导致：

- 用户请求被分配到已故障的渠道 → 报错 → 重试 → 浪费时间
- 并发高时大量请求打向坏渠道，整体成功率下降

## 🔧 改动说明

### 1. `controller/relay.go` — 渠道成败记录

```
+ model.RecordChannelSuccess(channel.Id, 0)   // 成功时记录
+ model.RecordChannelFailure(channel.Id)       // 失败时记录
```

每次 Relay 请求完成后，根据成功/失败调用对应记录函数。这是健康数据的来源。

### 2. `model/channel_cache.go` — 健康感知跳过

```
+ // 健康感知路由：跳过最近失败的渠道
+ if !IsChannelHealthy(channelId) {
+     continue
+ }
```

在 `GetRandomSatisfiedChannel`（渠道选择核心函数）中，按优先级筛选候选渠道时，**跳过最近失败的渠道**，避免把请求发给已知坏渠道。

## 📦 使用

```bash
# 1. 克隆原版
git clone https://github.com/Calcium-Ion/new-api.git
cd new-api

# 2. 应用补丁
git apply new-api-diff.patch

# 3. 正常构建
go build -o new-api .
```

## 📁 文件

```
├── controller/relay.go       # 改动后完整文件（含成败记录逻辑）
├── model/channel_cache.go    # 改动后完整文件（含健康跳过逻辑）
├── new-api-diff.patch        # git diff 补丁（可直接 apply）
└── README.md
```

## 🙏 参考

- [Calcium-Ion/new-api](https://github.com/Calcium-Ion/new-api) — 上游项目
