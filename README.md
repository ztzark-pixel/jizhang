# BOC 记账（网页版）—— BOC SG 支出自动记账

单文件网页 App（[index.html](index.html)），无需 Mac、无需安装，部署到 GitHub Pages 后配合 iPhone「快捷指令」实现 BOC 新加坡消费短信自动记账。

## 功能

- **短信自动记账**：快捷指令收到 BOC SG 短信 → 自动打开本页 → 解析金额/商户/时间 → 确认记账（或设置里开启"直接记账"跳过确认）
- **智能分类**：内置新加坡常见商户关键词（Cold Storage/NTUC→日用、BUS/MRT/Grab→交通、Ya Kun/PastaMania→餐饮等）；你手动纠正一次分类后，同一商户下次自动记住
- **重复保护**：同一条短信只会记一次
- **月度统计**：本月支出、分类占比饼图、月份左右切换
- **预算**：每月总预算 + 分类预算，超 80% 变橙、超支变红
- **数据安全**：全部存手机浏览器本地（localStorage），不上传任何服务器；支持导出 CSV（Excel 可开）和 JSON 备份/恢复
- 深色模式自动跟随系统

## 部署到 GitHub Pages（手机或电脑都能完成，约 10 分钟）

1. 注册/登录 [github.com](https://github.com)
2. 新建仓库（Repository）：名字随意，比如 `boctracker`，选 **Public**
3. 在仓库页面点 **Add file → Upload files**，上传本文件夹的 `index.html`，Commit
4. 仓库 **Settings → Pages**：Source 选 `Deploy from a branch`，Branch 选 `main` + `/ (root)`，Save
5. 等 1-2 分钟，你的 App 地址就是：`https://<你的用户名>.github.io/boctracker/`
6. 在 iPhone Safari 打开这个地址确认能用

> 注意：不要用 Safari 的「添加到主屏幕」打开使用——iOS 会把主屏幕版当成独立 App，数据和 Safari 里不互通，会导致快捷指令记的账在主屏幕版里看不到。日常直接在 Safari 里打开（可加书签/收藏）。

## 配置快捷指令自动化（在 iPhone 上）

1. 打开「快捷指令」App → 底部「自动化」→ 右上角 **+**
2. 触发条件选「**信息**」：
   - 发件人：选 **BOC SG**
   - 信息包含：`spending of SGD`（过滤掉 OTP 验证码、余额提醒）
   - 选「**立即运行**」（即关闭"运行前询问"）
3. 添加动作「**URL 编码**」，输入选变量「快捷指令输入」
4. 添加动作「**打开 URL**」，内容填：
   `https://<你的用户名>.github.io/boctracker/?raw=` 后面直接拼上变量「URL 编码的文本」
5. 完成保存

之后每次刷 BOC 卡消费，短信一到，Safari 会自动打开记账页：默认弹出确认页（金额/商户/分类都填好了，点一下"记账"即可）；如果在 App 设置里打开「短信识别后直接记账」，则完全零操作，自动入账并弹提示。

App 内「设置」页也有这份指南，并会自动显示你部署后的实际 URL。

## 本地开发测试（Windows）

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File serve.ps1
# 然后浏览器打开 http://localhost:8787/
```

## 短信解析格式

已按真实 BOC SG 短信样本校准并测试通过：

> There was an earmarked amount of SGD 52.30 for spending of SGD 52.30 at COLD STORAGE-ONE NORTH Singapore SGP on 05/07/2026 20:21 for your debit card ending with 1265. ...

- 金额取 `spending of SGD X`；商户取 `at ... on` 之间并去掉 `Singapore SGP` 后缀；日期按新加坡 DD/MM/YYYY 解析
- 含 refund / credited / deposited / received 的短信视为入账，不记支出
- 同时保留中文版中行短信的兜底解析

## 已知限制

- iOS 不允许任何网页/App 后台读短信，「快捷指令 + 打开 URL」已是系统允许的上限；短信到达时会短暂打开 Safari，属正常现象
- localStorage 理论上会在「手动清除 Safari 网站数据」时被清空——请养成每月导出 JSON 备份的习惯（设置页一键导出）
- 如果 BOC 日后修改短信文案导致识别失败，App 会自动弹出手动确认页兜底，不会丢账
