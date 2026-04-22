# 予約管理機能 要件定義（管理者）

## 機能概要

管理者が利用者から申請された予約を承認・却下する機能を提供する。
ダッシュボードで予約状況の統計を確認でき、フィルタリングによる一覧管理が可能。

---

## EARS 要件

### ダッシュボード

REQ-RESV-ADMIN-001
: adminユーザーが「GET /admin/dashboard」にアクセスしたとき、システムはpending・confirmed・rejectedの各予約総件数を含む予約統計を表示する。

REQ-RESV-ADMIN-002
: システムは、直近10件の予約申請を申請日時の降順でダッシュボードに表示する。

REQ-RESV-ADMIN-003
: システムは、ダッシュボードに各予約の申請者名・対象スロット日付・現在のステータスを表示する。

### 予約一覧

REQ-RESV-ADMIN-004
: adminユーザーが「GET /admin/reservations」にアクセスしたとき、システムは全予約申請を一覧形式で表示する。

REQ-RESV-ADMIN-005
: システムは、一覧に各予約の申請者名・スロット日付・希望開始時刻・希望終了時刻・ステータスを表示する。

REQ-RESV-ADMIN-006
: adminユーザーが「GET /admin/reservations」でステータスフィルタを適用したとき、システムは選択されたステータス（pending / confirmed / rejected）に一致する予約のみを表示する。

REQ-RESV-ADMIN-007
: システムは、予約一覧ページに「すべて」「pending」「confirmed」「rejected」のステータスフィルタオプションを提供する。

### 予約承認

REQ-RESV-ADMIN-008
: adminユーザーが「POST /admin/reservations/{reservation_id}/confirm」で承認リクエストを送信したとき、システムは予約ステータスを「confirmed」に更新し、予約一覧ページへリダイレクトする。

REQ-RESV-ADMIN-009
: adminユーザーが存在しない予約IDで「POST /admin/reservations/{reservation_id}/confirm」に承認リクエストを送信したとき、システムは404レスポンスを返す。

REQ-RESV-ADMIN-010
: adminユーザーが「POST /admin/reservations/{reservation_id}/confirm」でCSRFトークンなしまたは無効なCSRFトークンの承認リクエストを送信したとき、システムはリクエストを403レスポンスで拒否する。

### 予約却下

REQ-RESV-ADMIN-011
: adminユーザーが「POST /admin/reservations/{reservation_id}/reject」で却下リクエストを送信したとき、システムは予約ステータスを「rejected」に更新し、予約一覧ページへリダイレクトする。

REQ-RESV-ADMIN-012
: adminユーザーが存在しない予約IDで「POST /admin/reservations/{reservation_id}/reject」に却下リクエストを送信したとき、システムは404レスポンスを返す。

REQ-RESV-ADMIN-013
: adminユーザーが「POST /admin/reservations/{reservation_id}/reject」でCSRFトークンなしまたは無効なCSRFトークンの却下リクエストを送信したとき、システムはリクエストを403レスポンスで拒否する。

---

## 関連エンドポイント

| メソッド | パス | 説明 |
|---------|------|------|
| GET | `/admin/dashboard` | 予約統計・直近10件表示 |
| GET | `/admin/reservations` | 予約一覧（フィルタ可） |
| POST | `/admin/reservations/{id}/confirm` | 予約承認処理 |
| POST | `/admin/reservations/{id}/reject` | 予約却下処理 |

---

## データ仕様

### reservations テーブル

| カラム | 型 | 必須 | 説明 |
|--------|-----|------|------|
| id | INTEGER | ○ | 主キー（自動採番） |
| slot_id | INTEGER | ○ | 対象スロットID（外部キー） |
| user_id | INTEGER | ○ | 申請ユーザーID（外部キー） |
| desired_start | TIMESTAMP | ○ | 希望開始日時（ISO 8601） |
| desired_end | TIMESTAMP | ○ | 希望終了日時（ISO 8601） |
| status | TEXT | ○ | ステータス（pending / confirmed / rejected） |

### ステータス遷移

```
pending → confirmed（管理者が承認）
pending → rejected（管理者が却下）
```
