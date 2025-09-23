# 4. 資料模型 (Data Models)

## 4.1. Task (任務)

- **用途**: 代表使用者建立的單一待辦事項。
- **關鍵屬性**:
  - `id`: Integer - 主鍵
  - `title`: String - 任務標題
  - `status`: String - 狀態 (pending, completed)
  - `created_at`: Timestamp - 建立時間
- **關聯**: 一個任務可以有多個 TimeLog。

## 4.2. TimeLog (時間紀錄)

- **用途**: 代表一個已完成的番茄鐘專注時間段。
- **關鍵屬性**:
  - `id`: Integer - 主鍵
  - `task_id`: Integer - 外鍵，關聯至 Task
  - `duration_minutes`: Integer - 持續時間（分鐘）
  - `completed_at`: Timestamp - 完成時間
- **關聯**: 每個 TimeLog 屬於一個 Task。
