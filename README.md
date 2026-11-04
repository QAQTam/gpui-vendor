# gpui (vendored from zed)

[`gpui`](https://gpui.rs) 及其平台后端的独立提取版,作为通用 UI 框架库使用。

## Provenance(来源与钉死的 rev)

- 上游仓库: <https://github.com/zed-industries/zed>
- 钉死的源码 rev: `e21ff1b368b074586e4dae0d9bbf15d5dd8a1476`(main 分支,2026-11-05)
- 提取内容为上述 rev 的**逐字节原样拷贝**(verbatim copy),仅以下文件为本仓库新生成:
  - 本 README、根 `Cargo.toml`、`rust-toolchain.toml`、`.gitignore`
  - `assets/fonts/` 仅保留了上游 examples(wasm)实际引用的两个字体目录
- 根 `Cargo.toml` 中只保留 gpui 家族实际用到的 `[workspace.dependencies]`,
  以及 gpui 依赖图中真实生效的 `[patch.crates-io]`
  (calloop / async-task / async-process / khronos-egl / windows-capture)。

## 许可证(为什么这里是干净的)

- 上游 README:"Zed source code is licensed primarily under GPL-3.0-or-later,
  **with Apache-2.0 components where marked**"。本仓库提取的全部 crate 都属于
  marked as Apache-2.0 的部分:每个 crate 的 `Cargo.toml` 均声明
  `license = "Apache-2.0"`,且目录内自带 `LICENSE-APACHE`。
- 已核对:gpui 家族源码中不含任何 GPL 代码。
- `assets/fonts/` 中 IBM Plex Sans 与 Lilex 均为 SIL Open Font License 1.1,可自由再分发。
- 上游其余部分(编辑器本体等)仍是 GPL-3.0,不属于本仓库。

## Contents

- **核心**: `crates/gpui` — GPU 加速 UI 框架核心(实体/元素/布局/文本/输入/动作)
- **入口 facade**: `crates/gpui_platform` — 提供 `application()`,按目标平台自动接后端
- **平台后端**: `gpui_macos` / `gpui_ios` / `gpui_apple` / `gpui_windows` /
  `gpui_linux`(wayland + x11)/ `gpui_wgpu`(wgpu + cosmic-text 渲染器)/ `gpui_web`
- **支撑 crate**: `gpui_macros` / `gpui_shared_string` / `gpui_util` / `collections` /
  `http_client` / `refineable`(+`derive_refineable`)/ `scheduler` / `sum_tree` /
  `ztracing` / `ztracing_macro` / `zlog` / `bench_metrics` / `reqwest_client`(dev)/ `util_macros`(dev)

注意:gpui 核心自上游拆分后是**平台无关**的,单独依赖它无法创建窗口;
消费方必须同时依赖 `gpui_platform`(或直接使用某个平台后端 crate)。

## 在你的项目中使用

```toml
[dependencies]
gpui = { path = "../gpui/crates/gpui" }
gpui_platform = { path = "../gpui/crates/gpui_platform" }
```

若本仓库推送到 git 远端,可按 rev 钉死:

```toml
[dependencies]
gpui = { git = "ssh://git@your-host/you/gpui", rev = "<commit-of-this-snapshot>" }
gpui_platform = { git = "ssh://git@your-host/you/gpui", rev = "<commit-of-this-snapshot>" }
```

Linux(Wayland/X11)最小启动示例见 `crates/gpui/examples/hello_world.rs`:

```rust
use gpui_platform::application;

fn main() {
    application().run(|cx: &mut gpui::App| {
        cx.open_window(Default::default(), |_window, cx| {
            cx.new(|_cx| MyView)
        }).unwrap();
        cx.activate(true);
    });
}
```

## 同步到新的上游 rev

1. 在上游选定新 rev;
2. 对上面 Contents 列出的 crate 目录重跑逐字节拷贝(本 README 的生成逻辑同样适用);
3. 重新核对根 `Cargo.toml` 的 `[workspace.dependencies]` / `[patch.crates-io]` 是否需要增删;
4. 在本 README 顶部更新 Provenance 的 rev 与日期,并 commit —— 一次 commit 对应一次上游快照。
