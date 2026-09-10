# 2026f rCore：从 Fork 到自动评测

这份流程适用于个人 GitHub 账号。课程编号为 **2073**，实验源码基于 LearningOS 2026s rCore；评测由个人仓库的 GitHub Actions 执行。

## 1. 准备账号

在 OpenCamp 登录并加入课程 2073 所属训练营，绑定自己实际使用的 GitHub 账号。成绩按 GitHub **登录名**识别，不按显示昵称或邮箱识别。

下面的本地提交步骤使用 SSH，请先配置好自己 GitHub 账号的 SSH 公钥。

## 2. Fork 完整仓库

打开 [2026f rCore 课程仓库](https://github.com/Alayfolk64/2026f-rcore)，点击 **Fork**。

- Owner 选择自己的个人账号，仓库名保留 `2026f-rcore`。
- **取消勾选 `Copy the main branch only`**，复制全部章节分支。
- 创建后检查分支列表，确认包含 `main` 和 `ch1` 至 `ch8`。

这个选项决定是否复制章节代码，见 [GitHub Fork 文档](https://docs.github.com/en/pull-requests/how-tos/work-with-forks/fork-a-repo)。不要只下载 ZIP，也不需要向课程仓库提交 PR。

## 3. 启用 Actions

进入自己 Fork 的 **Actions** 页面，按页面提示启用工作流。入口名称通常为 **I understand my workflows, go ahead and enable them**。

可以看到名为 **rCore 2026f grading** 的工作流。参考 [GitHub 工作流启用说明](https://docs.github.com/en/actions/how-tos/manage-workflow-runs/disable-and-enable-workflows)。

## 4. 配置成绩上传 Token

进入自己仓库的 **Settings → Secrets and variables → Actions → New repository secret**。

| 字段 | 填写内容 |
| --- | --- |
| Name | `ARCEOS_2026_SPRING_TOKEN` |
| Secret | 管理员为本课程提供的成绩上传 Token |

这里沿用已验证的 Token 和 Secret 名称。名称中的 `ARCEOS_2026_SPRING` 是现有凭证名称，**本仓库实际提交的课程固定为 2073**。

Fork 不会把上游的 Secret 复制过来，每位学员都必须在自己的仓库配置。不要把 Token 写进代码、README、Issue 或提交记录。`GITHUB_TOKEN` 由 GitHub 自动提供，不需要手动添加；不需要另外配置课程 ID 或 API 地址。见 [GitHub Secrets 文档](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-secrets)。

## 5. 克隆并完成第一项实验

把下列地址中的 `YOUR_GITHUB_LOGIN` 换成自己的 GitHub 登录名。

```sh
git clone git@github.com:YOUR_GITHUB_LOGIN/2026f-rcore.git
```

克隆自己的 Fork，使用当前账号的 SSH 身份认证。

```sh
cd 2026f-rcore
```

进入本地仓库根目录。

```sh
git switch ch3
```

切换到第 3 章实验分支。按照 [rCore 实验指导](https://learningos.github.io/rCore-Tutorial-Guide/) 完成代码，并提交真实的 `reports/lab1.md` 或 `reports/lab1.pdf` 实验报告。

```sh
git diff
```

检查自己的源码和报告修改。

```sh
git add os reports
```

把实验代码和报告加入本次提交；需要新增其他实验文件时，把相应路径明确加入。

```sh
git commit -m "Complete rCore chapter 3"
```

保存本次修改，`-m` 指定提交说明。

```sh
git push origin ch3
```

推送到自己仓库的 `ch3` 分支，并触发该章评测。

## 6. 查看评测与成绩

进入自己仓库的 **Actions**，打开刚触发的运行。

1. **Test chapter and reports**：运行 QEMU 和官方检查器，检查测试及实验报告。只有全部测试通过、报告齐全、检查器退出状态为 0 时，该章才通过。
2. **Save progress and upload score**：记录已通过章节，将累计成绩上传到 OpenCamp。日志显示 `OpenCamp accepted the score (result=1).` 表示接口接受了成绩。
3. 返回 OpenCamp 的学员成绩页面刷新，核对自己账号与课程。接口接受成绩与网页实际显示是两个验收步骤。

运行附件包含 `rcore-grade.log` 和 `rcore-result.json`，保留 30 天。成功记录保存在自己仓库 `gh-pages` 分支的 `course-2073.json`；无需开启 GitHub Pages 网站服务。

只推送未完成的模板代码时，测试失败是正常结果，不会上传通过成绩。

## 7. 按章节继续提交

| 分支 | 该章分值 | 必须保留的实验报告 |
| --- | ---: | --- |
| `ch3` | 100 | `lab1` |
| `ch4` | 100 | `lab1`、`lab2` |
| `ch5` | 100 | `lab1`、`lab2`、`lab3` |
| `ch6` | 100 | `lab1` 至 `lab4` |
| `ch8` | 100 | `lab1` 至 `lab5` |

报告放在 `reports/` 下，使用 `.md` 或 `.pdf`。切换下一章后检查此前报告是否仍然存在；各章节分支独立，报告需要一并保留。不要为同步报告而合并整条章节分支，以免混入不同章的内核代码。

每章全部通过后记 100 分，总分 500；重复通过同一章不会重复加分。已通过章节的成绩保留，之后失败的提交不会扣除此前分数。

只有 `ch3`、`ch4`、`ch5`、`ch6`、`ch8` 的 push 自动评分；`main`、`ch1`、`ch2`、`ch7` 不评分。也可以在 Actions 中选择 **Run workflow**，并选择对应实验分支；选择 `main` 会跳过评分。

## 常见问题

| 现象 | 检查与处理 |
| --- | --- |
| 只有 `main`，没有章节 | Fork 时没有复制全部分支；先补齐章节分支再提交实验 |
| push 后没有运行 | 检查 Actions 是否启用、推送的是否为五个评分分支、分支内是否存在 `.github/workflows/build.yml` |
| 测试没有全部通过 | 打开测试日志定位失败项，修改实验代码后重新 push |
| 已显示 N/N，任务仍失败 | 检查后续报告检查及检查器退出状态，N/N 本身不足以通过 |
| `ARCEOS_2026_SPRING_TOKEN is missing` | 在自己的 Fork 中添加同名 Repository secret |
| `user is not join` | 检查 OpenCamp 已加入对应训练营，并绑定当前仓库所有者的 GitHub 登录名 |
| 上传接口返回其他错误 | 保留日志中的返回内容，交由课程管理员核对凭证和课程配置 |
| 写入 `gh-pages` 返回 403 | 检查仓库或组织 Actions 策略是否允许工作流的 `contents: write` 权限 |
| 上传任务被跳过 | 本流程只允许个人仓库所有者推送时上传成绩；协作者或机器人触发的运行只测试 |

完成账号或 Secret 配置后，可以对原运行选择 **Re-run failed jobs**。上传失败时已通过的章节记录仍保留，重试不会重复计分。
