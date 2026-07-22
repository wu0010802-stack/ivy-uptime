# ivy-uptime

Ivy 管理系統的 uptime 監控（GitHub Actions 排程，免外部服務）。

| Workflow | 頻率 | 監控對象 | 失敗時 |
|----------|------|---------|--------|
| `prod-uptime` | 每 5 分鐘* | 後端 `/health/ready`（含 DB）+ 前端 `ivypreschool.tw` | 寄 email + 自動開 `incident` issue；恢復後自動關閉 |
| `staging-uptime` | 每 30 分鐘 | staging 後端 `/health/ready` | 寄 email（開發期紅燈可到 Actions 頁一鍵 Disable） |

\* GitHub cron 為盡力交付，實際間隔常為 5–15 分鐘。

> ⚠ GitHub 會在 repo **60 天無 commit** 後自動停用排程 workflow，停用前約一週會寄警告信——
> 收到就點「Keep workflows active」，或對本 repo 做任意一個 commit 即可重置計時。

## 事件處理

1. 收到失敗通知 → 看 incident issue 內連結的 run log，分辨是後端（503 = 維護模式 / DB 掛）還是前端
2. 後端問題 → Zeabur dashboard 看 `ivymanagesystem-backend` runtime log；Sentry 看錯誤事件
3. 誤報（GitHub runner 網路抖動）→ workflow 內建 curl retry 3 次，連續失敗才會紅，誤報率低

## 相關文件

- 工作流程與啟用紀錄：workspace `docs/sop/uptime-monitor-setup.md`
- 若要升級為秒級偵測 + LINE 告警：改用 UptimeRobot（見同一份 SOP）
