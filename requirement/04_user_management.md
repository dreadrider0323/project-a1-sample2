# ユーザー管理機能 要件定義（管理者）

## 機能概要

管理者がシステムを利用するユーザーアカウントを作成・削除する機能を提供する。
ユーザーにはロール（admin / user / viewer）を割り当て、アクセス権限を制御する。

---

## EARS 要件

### ユーザー一覧

REQ-USER-MGMT-001
: adminユーザーが「GET /admin/users」にアクセスしたとき、システムは登録済みの全ユーザーアカウントの一覧を表示する。

REQ-USER-MGMT-002
: システムは、ユーザー一覧に各ユーザーのユーザー名・氏名・ロールを表示する。

### ユーザー作成

REQ-USER-MGMT-003
: adminユーザーが有効なユーザー名・パスワード・氏名・ロールを「POST /admin/users/new」でユーザー作成フォームとして送信したとき、システムは新しいユーザーアカウントを作成し、ユーザー一覧ページへリダイレクトする。

REQ-USER-MGMT-004
: adminユーザーが「POST /admin/users/new」でデータベースに既に存在するユーザー名のユーザー作成フォームを送信したとき、システムはリクエストを拒否してユーザー名重複エラーを表示する。

REQ-USER-MGMT-005
: adminユーザーが「POST /admin/users/new」で必須フィールドが欠落したユーザー作成フォームを送信したとき、システムはリクエストを拒否してバリデーションエラーを表示する。

REQ-USER-MGMT-006
: adminユーザーが「POST /admin/users/new」でCSRFトークンなしまたは無効なCSRFトークンのフォームを送信したとき、システムはリクエストを403レスポンスで拒否する。

REQ-USER-MGMT-007
: システムは、新規ユーザー作成時のロール値として「admin」「user」「viewer」のみを受け付ける。

REQ-USER-MGMT-008
: システムは、新規ユーザー作成時に提供されたパスワードをデータベースへ保存する前にbcryptでハッシュ化する。

### ユーザー削除

REQ-USER-MGMT-009
: adminユーザーが「POST /admin/users/{user_id}/delete」で削除リクエストを送信したとき、システムは指定されたユーザーアカウントをデータベースから削除し、ユーザー一覧ページへリダイレクトする。

REQ-USER-MGMT-010
: adminユーザーが「POST /admin/users/{user_id}/delete」で自分自身のアカウントを削除しようとした場合、システムはリクエストを拒否してエラーメッセージを表示する。

REQ-USER-MGMT-011
: adminユーザーが存在しないユーザーIDで「POST /admin/users/{user_id}/delete」に削除リクエストを送信したとき、システムは404レスポンスを返す。

REQ-USER-MGMT-012
: adminユーザーが「POST /admin/users/{user_id}/delete」でCSRFトークンなしまたは無効なCSRFトークンの削除リクエストを送信したとき、システムはリクエストを403レスポンスで拒否する。

---

## 関連エンドポイント

| メソッド | パス | 説明 |
|---------|------|------|
| GET | `/admin/users` | ユーザー一覧表示 |
| POST | `/admin/users/new` | ユーザー作成処理 |
| POST | `/admin/users/{id}/delete` | ユーザー削除処理 |

---

## データ仕様

### users テーブル

| カラム | 型 | 必須 | 説明 |
|--------|-----|------|------|
| id | INTEGER | ○ | 主キー（自動採番） |
| username | TEXT | ○ | ログイン名（一意制約） |
| password_hash | TEXT | ○ | bcryptハッシュ |
| role | TEXT | ○ | ロール（admin / user / viewer） |
| full_name | TEXT | ○ | 表示名・氏名 |

---

## 制約事項

- 管理者は自分自身のアカウントを削除できない
- ユーザー名は一意でなければならない
- パスワードはプレーンテキストで保存せず、必ずbcryptハッシュで保存する
