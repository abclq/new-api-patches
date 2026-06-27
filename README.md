# new-api 本地修改补丁

配合 [Calcium-Ion/new-api](https://github.com/Calcium-Ion/new-api) 使用。

## 修改内容

| 文件 | 改动 | 说明 |
|------|:--:|------|
| `controller/relay.go` | +2 行 | 渠道缓存优化 |
| `model/channel_cache.go` | +5 行 | 渠道缓存逻辑调整 |

## 使用

```bash
# 克隆原版
git clone https://github.com/Calcium-Ion/new-api.git
cd new-api

# 应用补丁
git apply new-api-diff.patch
```

或直接替换对应文件。
