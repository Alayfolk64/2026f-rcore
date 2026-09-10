# 2026f rCore 验证记录

本记录区分测试逻辑、真实检查器执行、GitHub 运行和 OpenCamp 接口，不把模拟验证写成真实成绩上传。

## 已完成的基础检查

- Python 语法和工作流 YAML 语法检查通过。
- 章节成绩可按任意顺序累计，同一章节重试不重复计分，五章最高 500 分；不完整的通过结果以及其他用户的历史记录被拒绝。
- 在本地独立 Git 仓库中实际执行了 `gh-pages` 创建、章节记录提交和推送；覆盖上传拒绝后重试与五章累计。只有 HTTP 调用使用模拟响应，未向 OpenCamp 写入测试分数。
- 上传接口的非 JSON 响应和 `result` 不为 1 的响应均视为失败。

回归测试入口：

```sh
python3 .github/tests/test_grading.py
```

在仓库根目录执行，检查官方随机后缀输出解析、非法结果拒绝、章节累计及接口业务错误处理；不会访问网络或上传成绩。

## 真实检查器验证

使用原有已完成的 ch3 作业在独立本地 checkout 验证，代码没有加入课程模板。镜像为 `alicesama/rcore-ci:2024a`，拉取摘要为 `sha256:6e5706f0cfb8e5d3cf705d6c1069b42865e6ca11c5945a38588d58c2bad903e4`。

首次执行 QEMU 测试得到 `Test passed10564: 7/7`，报告检查成功，官方检查器退出状态 0。包装脚本最初只识别无后缀的 `Test passed:`，导致误判失败；已修正为兼容官方随机数字后缀，并添加真实输出回归用例。

修正后从全新 checkout 再次完整执行，实际结果为 `Test passed54729: 7/7`、`Report for lab1 found.`，包装脚本退出状态 **0**，结果为 `passed: true`、`points: 7/7`。因此编译、QEMU、官方检查器、报告检查和结果解析的正向链路已经实际通过。

## 仓库与 GitHub 验证

`main` 和 `ch1` 至 `ch8` 已推送至 [Alayfolk64/2026f-rcore](https://github.com/Alayfolk64/2026f-rcore)。逐分支与 2026s 上游提交比较，差异仅为 CI、课程文档和忽略规则，章节实验源码没有修改。各分支的评分工作流和脚本保持一致。

已在仓库设置中添加 `ARCEOS_2026_SPRING_TOKEN`，GitHub 显示 `Repository secret added.`；公开文件不包含凭证值。

2026-09-11 实际向五个评分分支 push，全部自动触发。以下结果来自未完成实验的上游模板，**测试失败是预期结果**：

| 分支 | 官方测试通过数 | 检查器退出状态 | GitHub 运行 |
| --- | --- | ---: | --- |
| `ch3` | 5/7 | 2 | [运行记录](https://github.com/Alayfolk64/2026f-rcore/actions/runs/34525042217) |
| `ch4` | 4/16 | 2 | [运行记录](https://github.com/Alayfolk64/2026f-rcore/actions/runs/34525126176) |
| `ch5` | 2/15 | 2 | [运行记录](https://github.com/Alayfolk64/2026f-rcore/actions/runs/34525135684) |
| `ch6` | 2/31 | 2 | [运行记录](https://github.com/Alayfolk64/2026f-rcore/actions/runs/34525146286) |
| `ch8` | 22/25 | 2 | [运行记录](https://github.com/Alayfolk64/2026f-rcore/actions/runs/34525167984) |

五次运行均成功拉取镜像、检出源码、执行官方检查器并保存日志及 JSON 附件；失败结果被正确解析，成绩上传作业全部跳过。`ch3` 原始失败包括：

```text
Panicked at src/bin/ch3_trace.rs:22, assertion failed: 3 <= count_syscall(SYSCALL_GETTIMEOFDAY)
Test passed14832: 5/7
AssertionError
make: *** [Makefile:118: test] Error 1
Process completed with exit code 2.
```

这证明未完成的模板不会被误判为通过。对应的完整通过场景已在上面的本地独立 checkout 实测为 7/7；未把已完成作业发布到模板，也未让模板仓库上传练习成绩。

## OpenCamp 同步验证范围

此前课程 2073 的基础练习联调使用同一个 Token，实际调用接口得到 `OpenCamp accepted the score (result=1).`，证明当时该账号加入训练营后接口接受了请求。该次为 **0/100 的基础练习**，不是本套 rCore 的五章 500 分验收。

本模板按 rCore 原规则配置 500 分累计。未将模拟的 500 分上传到真实课程，也未宣称已经核对 OpenCamp 网页上的 500 分显示。
