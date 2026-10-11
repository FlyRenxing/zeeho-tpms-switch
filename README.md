# ZEEHO 胎压设置开关

给 [Stash](https://stash.ws) / [Loon](https://loon0x00.github.io) 用的脚本：拦截极核 App 的 `vehicleSet/items` 和 `itemsV2`，在真实响应里插入 `functionCode: TIRE_MONITORING`，让车辆设置出现「胎压传感器」入口。

只改这个显示开关。不 mock 胎压读数、预警范围、首页或 `carSet`。

## 安装

先在客户端里开启 **MitM**，安装并信任 CA 证书，再导入插件。流量要走该客户端。

Loon 插件里的脚本走 jsDelivr。国内 GitHub raw 经常拉不下来。

### Stash

覆写 URL（jsDelivr）：

```text
https://cdn.jsdelivr.net/gh/FlyRenxing/zeeho-tpms-switch@main/stash/zeeho-tpms-switch.stoverride
```

Stash → 配置 → 覆写 → 从 URL 安装 → 打开该覆写。Stash 覆写内带 payload 兜底，装上就能跑。

GitHub raw：

```text
https://raw.githubusercontent.com/FlyRenxing/zeeho-tpms-switch/main/stash/zeeho-tpms-switch.stoverride
```

### Loon

插件 URL（jsDelivr）：

```text
https://cdn.jsdelivr.net/gh/FlyRenxing/zeeho-tpms-switch@main/loon/zeeho-tpms-switch.plugin
```

Loon → 配置 → 插件 → 添加。同时打开 **MitM**、**脚本**。添加后点一次插件更新。

GitHub raw：

```text
https://raw.githubusercontent.com/FlyRenxing/zeeho-tpms-switch/main/loon/zeeho-tpms-switch.plugin
```

## 使用

1. 完全杀掉极核 App（从后台划掉）。
2. 重新打开，进入 **车辆设置**（不要只停在首页）。
3. 应能看到「胎压传感器」。

Loon 日志里生效会长这样：

- `Load plugin:ZEEHO 胎压设置开关`
- `Match MITM host:tapi.zeehoev.com`
- URL 含 `vehicleSet/items` 或 `vehicleSet/itemsV2`
- 脚本日志：`[ZEEHO TPMS] ... rewritten`

## 改了什么

| 项 | 值 |
| --- | --- |
| Host | `tapi.zeehoev.com` |
| 首页能力列表 | `GET .../vehicleSet/items`，`data[]` 插入 `TIRE_MONITORING` |
| 车辆设置宫格 | `GET .../vehicleSet/itemsV2`，插入能力 + 「胎压传感器」 |

脚本透传服务器原文，只在缺少该 `functionCode` 时插入。已经有这个开关的车不会重复插入。

App 侧：外置胎压设置页看 `TIRE_MONITORING`；`builtInTirePressure=true` 时仍会隐藏绑定/设置箭头。本脚本不改首页和 `carSet`。

## 仓库结构

```text
scripts/zeeho-tpms-switch.js          共用脚本（Stash / Loon / Surge）
stash/zeeho-tpms-switch.stoverride    Stash 覆写（含 payload 兜底）
loon/zeeho-tpms-switch.plugin         Loon 插件
```

## License

MIT
