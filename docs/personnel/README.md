# 人员责任包索引

本目录按人员拆分项目资料。每个人的目录包含：

- `README.md`：个人职责、边界、阅读顺序和交付要求。
- `00-project-overview.md`：项目总览。
- `01-file-module-split.md`：全项目模块和文件边界。
- `02-personnel-task-plan.md`：全体人员分工表。
- `modules/`：该人员负责或协作模块的文档副本。
- `worklogs/`：该人员相关模块的当前工作日志副本。
- `templates/`：交接单和进度报告模板。

总控原始文档仍保留在 `docs/project`、`docs/modules`、`docs/worklogs`。本目录是给个人执行和交接使用的资料包，不替代总控目录。

## 人员目录

| 人员 | 主负责 | 协作 |
| --- | --- | --- |
| `凝言` | 引擎兼容、UI 重做 | UI 动画、图片重制、打包 |
| `盐甘草糖块` | 1920x1080 分辨率、性能 | 剧本解析、UI 重做 |
| `yuuki` | 剧本解析、UI 动画 | 引擎兼容、性能 |
| `happy` | 打包、图片 QA、功能验收 | 安装验收 |
| `虹映千夏` | 图片重制 | UI 重做、图片 QA |
| `himahima` | 图片 QA | 图片返修确认 |
| `03r` | 图片重制、waifu2x 流水线 | 图片 QA |

## 使用规则

1. 每个人优先阅读自己目录下的 `README.md`。
2. 需要跨模块修改时，先填写 `templates/04-handoff-template.md`。
3. 每次阶段性完成后，填写 `templates/05-progress-report-template.md`。
4. 个人目录下的模块文档是副本；总控更新以 `docs/modules` 为准，必要时由负责人同步。
