# Storage Redirection X 发布说明

本仓库仅用于发布安装包与模块包，不包含源码。

## 一、文件用途

- `storage.redirect.x_<版本>-arm64-v8a.apk`
  - Android 应用安装包（真机常用）。
- `storage.redirect.x_<版本>-x86_64.apk`
  - Android 应用安装包（模拟器常用）。
- `storage-redirect-x_v<版本>-zygisk.zip`
  - Zygisk 模块包（用于 Magisk/KernelSU）。

## 二、安装顺序

1. 安装并打开 APK。
2. 在 Magisk 或 KernelSU 中安装 `storage-redirect-x_v<版本>-zygisk.zip`。
3. 重启设备。
4. 回到应用内检查模块状态并进行配置。

## 三、手动配置位置

模块配置目录如下：

- `/data/adb/modules/storage.redirect.x/config/`

主要文件：

- 全局配置：`/data/adb/modules/storage.redirect.x/config/global.json`
- 应用配置目录：`/data/adb/modules/storage.redirect.x/config/apps/`
- 单应用配置示例：`/data/adb/modules/storage.redirect.x/config/apps/com.example.app.json`

## 四、手动配置步骤

### 1) 修改全局配置

编辑 `global.json`，控制是否全局监控。

示例：

```json
{
  "monitor_all_apps": true
}
```

### 2) 添加应用规则

在 `apps/` 目录下创建 `<包名>.json`。

示例（`com.example.app.json`）：

```json
{
  "users": {
    "0": {
      "enabled": true,
      "allowed_real_paths": [
        "Download",
        "Pictures"
      ],
      "path_mappings": {
        "Documents": "Download"
      }
    }
  }
}
```

说明：

- `users` 的 key 是 Android 用户 ID（常见主用户为 `0`）。
- `allowed_real_paths` 与 `path_mappings` 使用相对路径，不要以 `/` 开头。

### 3) 让配置生效

- 修改配置后，重启目标应用。
- 如有需要，重启设备后再验证。

## 五、日志位置与排障

日志目录：

- `/data/adb/modules/storage.redirect.x/logs/`

常见日志文件：

- 运行日志：`/data/adb/modules/storage.redirect.x/logs/running.log`
- 文件监控日志：`/data/adb/modules/storage.redirect.x/logs/file_monitor.log`

常用排障命令（需 root）：

```sh
su
ls -l /data/adb/modules/storage.redirect.x/logs/
tail -n 200 /data/adb/modules/storage.redirect.x/logs/running.log
tail -n 200 /data/adb/modules/storage.redirect.x/logs/file_monitor.log
```

查看 SELinux 拒绝（可选）：

```sh
su
logcat -d | grep -i avc
```

## 六、注意事项

- 建议 APK 与 Zygisk 模块版本保持一致。
- 升级后建议重启设备，避免旧进程缓存配置。
- 发布会覆盖同名资产，请始终使用最新版本文件进行安装。
