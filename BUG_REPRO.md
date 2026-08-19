# Bug Reproduction

## 包的性质

当前 test_model_fix 保存的是被测模型修复后的结果源码，不是初始含 Bug 源码。要复现原始缺陷，必须检出下面固定的 parent SHA；不要在当前修复结果源码上期待重新出现修复前失败。生成系统使用的可信验证补丁和完整验证日志仅在本地留存，不提交到结果分支。

## 问题现象

同一推理运行连续出现多条越界质量观测后，漂移事件的观测数量和最小/最大分数始终停留在初始值。请先不要修改代码，定位聚合状态为什么没有进入持久化对象，并用两次上报结果证明。 调查全程不要修改目标仓库中的生产代码、测试代码或配置。

## 含 Bug 版本

- 仓库：zhanglei10281852-gif/ai-featuremesh-task-12
- 仓库地址：https://github.com/zhanglei10281852-gif/ai-featuremesh-task-12.git
- parent SHA：73ff03a994336d913ba7f876cd495b4a6e3da320

## 复现步骤

```bash
git clone -- https://github.com/zhanglei10281852-gif/ai-featuremesh-task-12.git bug-repro
cd bug-repro
git checkout --detach 73ff03a994336d913ba7f876cd495b4a6e3da320
go test ./internal/service -run "^TestDriftIncidentAccumulatesObservations$" -count=1
```

## 双架构完整错误信息

### linux/amd64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/service -run "^TestDriftIncidentAccumulatesObservations$" -count=1
--- FAIL: TestDriftIncidentAccumulatesObservations (0.51s)
    annotation_behavior_test.go:145: drift_incidents = {Items:[{ID:drift_7bce3f56b5d52d2397c9dd9f InferenceRunID:run_fce0d28dd0d213d2aae113a4 Status:open FirstObservationAt:0001-01-01 00:00:00 +0000 UTC LastObservationAt:0001-01-01 00:00:00 +0000 UTC Minimum:0.000 Maximum:0.000 ObservationCount:0 ReviewDueAt:2026-08-18 12:00:00 +0000 UTC CreatedAt:2026-08-18 08:00:00 +0000 UTC UpdatedAt:2026-08-18 08:00:00 +0000 UTC Version:2}] Total:1}
FAIL
FAIL	github.com/zhanglei10281852-gif/ai-featuremesh-base/internal/service	0.517s
FAIL

```

stderr：

```text
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644

```

### linux/arm64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/service -run "^TestDriftIncidentAccumulatesObservations$" -count=1
--- FAIL: TestDriftIncidentAccumulatesObservations (1.39s)
    annotation_behavior_test.go:145: drift_incidents = {Items:[{ID:drift_9887bb45b8bf3f3757533913 InferenceRunID:run_7775440d2819f49dfc871fb3 Status:open FirstObservationAt:0001-01-01 00:00:00 +0000 UTC LastObservationAt:0001-01-01 00:00:00 +0000 UTC Minimum:0.000 Maximum:0.000 ObservationCount:0 ReviewDueAt:2026-08-18 12:00:00 +0000 UTC CreatedAt:2026-08-18 08:00:00 +0000 UTC UpdatedAt:2026-08-18 08:00:00 +0000 UTC Version:2}] Total:1}
FAIL
FAIL	github.com/zhanglei10281852-gif/ai-featuremesh-base/internal/service	1.573s
FAIL

```

stderr：

```text
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644

```

## 通过条件

准确定位根因，指出具体 Go 文件和符号，解释错误行为如何导致题面症状，并给出实际复现、调用链或持久化证据；完成时目标仓库代码、测试和配置零改动。
