# DZJ3 KernelSU 3.3.1 KDP 实验版

仅限于：

- SM-S9280 国行（CHC）
- 固件 `S9280ZCS6DZJ3`
- 内核 `6.1.145-android14-11-3254743-abS9280ZCS6DZJ3`

KernelSU v3.3.1 官方代码 + 三星 KDP/RKP/DEFEX 补丁，用于 DZJ3 临时 root。

> 这不是刷机包，不写 boot/分区；真重启后 root 消失。

## 产物

Release：

https://github.com/Reallyliked21/dzj3-ksu-331-kdp/releases/tag/v3.3.1-dzj3-kdp-0.1

| 文件 | 说明 |
|---|---|
| `kernelsu-331-kdp-dzj3.ko` | 3.3.1 + KDP/RKP/DEFEX，DZJ3 vermagic |
| `ksud-331-kdp-dzj3` | 3.3.1 ksud，内嵌上面的 LKM；使用**官方完整 soft-reboot**（只建议 late-load） |
| `ksud-331-kdp-dzj3-modsoft` | 3.3.1 ksud，模块阶段 + zygote 软重启；App v3.5 内置版 |
| `SHA256SUMS.txt` | 校验值 |

LKM SHA-256：

```text
e5228933d940bbc07cfdd3aaf741f3ea2e70cecf801aa52195f3ad22d33d4bf3
```

Release 的 ksud SHA-256：

```text
6626e4d6da60c984b30e31c6440229831edc052e175e1477494122e9b7635c9e  ksud-331-kdp-dzj3
eb96471cbaacc15cb3faa82e1fd9455b3c7f87c22baf0ea1e5ff4c6f1df872cb  ksud-331-kdp-dzj3-modsoft
```

> 这个 ksud 的 soft-reboot 是官方 `stop/start`，在 KDP 上会真重启；只建议用它做 late-load。
> DZJ3 App v3.5+ 内置的是“模块阶段 + zygote”版，SHA-256 为
> `eb96471cbaacc15cb3faa82e1fd9455b3c7f87c22baf0ea1e5ff4c6f1df872cb`。

## 已验证

- 官方 KernelSU Manager v3.3.1 显示驱动 32680 / LKM
- `su` 正常
- LSPosed 正常加载
- App v3.5 内软重启不真重启，root/LKM 保留
- 模块脚本会重跑
- 不写分区

## 软重启

- **App v3.5+ 内的软重启按钮**：模块阶段 + 重启 zygote，不真重启，模块会生效；
- **KernelSU 管理器自带的软重启按钮**：仍走官方 stop/start，会真重启，请勿使用；
- 官方完整 soft-reboot 暂不推荐在 KDP 上使用。

## 构建

GitHub Actions 会自动：

1. 拉取 KernelSU v3.3.1；
2. 应用 `patches/0001-samsung-kdp.patch`；
3. 用 DDK 编译 LKM；
4. 可选应用 `patches/0002-soft-reboot-module-effective.patch` 构建 App 用 ksud。

Workflow：

```text
.github/workflows/build.yml
```

构建记录：

https://github.com/Reallyliked21/dzj3-ksu-331-kdp/actions

## 已知限制

- 实验版，长期稳定性未验证
- ADB Root 不支持（-38）
- 「隐藏 SELinux 修改」实际无效
- 管理器「刷入内核」不要点
- 管理器软重启按钮会真重启
- 仅 DZJ3

## 许可

- KernelSU：GPL-3.0
- BakaSU 三星补丁：MIT
- 完整许可见 `LICENSE` 与 `THIRD_PARTY_NOTICES.md`

## 风险

临时 root 与 KDP 适配属于非官方方案，可能导致重启、崩溃或不稳定；不要用于银行、数字人民币等金融环境。
