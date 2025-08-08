# 🎫 TickEasy 線上購票系統

TickEasy 是一個基於純 Java Web（Spring MVC + Servlet + JDBC）的線上購票專案，為2025年緯育Java在職專班的結訓專題，我主以 HTTP 原理、Session、Token 驗證與 JavaMail 發信機制作為主力。

---

## 📚 專案背景

- **專題緣起**：2025年緯育Java在職專班的結訓專題，旨在建立互動性高的演唱會平台，改善現行購票網站提醒不足、缺乏換票、黃牛氾濫等問題，提供即時通知與票券轉換功能。
- **個人貢獻**：
- 前台開發：會員註冊登入、會員資料編輯、Email驗證(註冊驗證/更改密碼驗證)、會員權限辨別導向頁面
- 後台開發：數據儀錶板(收入圓餅圖/銷售趨勢圖/橫向進度條)、報名人列表與明細
- **部署平台**：Heroku（Dyno 閒置會休眠，啟動約 20–30 秒）  
- **公開網址**：`https://your-ticketing-app.herokuapp.com/`  
- **購票者帳號**：`buyer1` / 123456
- **管理者帳號**：`eventhost1` / 123456

---

## ⚙️ 功能模組

| 編號 | 模組 | 說明 | 後端實作重點 / 技術關鍵詞 |
|------|------|------|--------------------------|
| **1** | **Member 會員管理** | 登入／註冊／登出／編輯會員資料，JavaMail 驗證信，Token 認證 | Spring MVC、Hibernate、SHA-256 密碼雜湊、Token、JavaMail、Redis 登入快取 |
| **2** | **EventDetail 管理** | Dashboard 銷售統計與報表，報名人列表與明細查詢／匯出 | Spring MVC、MySQL 聚合查詢、ApexCharts、RESTful API |
| 3 | 場次查詢 | 多條件搜尋（日期、場館、演出）並即時顯示剩餘座位 | Hibernate Criteria、多表關聯查詢、快取優化 |
| 4 | 線上購票 | 選擇場次與座位，加入購物車，結帳付款 | Spring 事務管理 |
| 5 | 訂單管理 | 訂單建立／查詢／取消，支援狀態變更 | Spring Transaction |
| 6 | 即時通知 | WebSocket 即時推播訂單狀態、剩餘票數 | WebSocket、STOMP 協定|

## 📌 負責模組
- **1. Member 會員管理**
- **2. EventDetail 管理**
---

## 🚀 演示（Demo）

- **會員註冊／驗證**  
  ![Member Register & Email](docs/member-register.gif)  
- **Dashboard 摘要**  
  ![Event Dashboard](docs/event-dashboard.gif)  
- **參與者列表**  
  ![Participant List](docs/participant-list.gif)  

---

## 🛠️ 技術棧

- **後端**：Java 8、Spring MVC、Servlet API  
- **安全**：JavaMailSender  
- **資料庫**：JDBC + HikariCP、MySQL、Redis（分布式鎖、快取）  
- **前端**：jQuery、Bootstrap、Ajax、WebSocket (STOMP)  
- **部署**：Tomcat 9.0 / Heroku

---

## 📂 專案結構

```text
TickEasy
├─ common/                # 系統共用模組（基礎設施層）
│   ├─ config/            # Spring MVC、Spring Data、WebSocket、排程等全域設定
│   ├─ controller/        # 共用 API，例如檔案上傳、系統狀態
│   ├─ dao/               # 共用資料存取邏輯（基礎 CRUD、Session 管理）
│   ├─ filter/            # 請求與 Hibernate 過濾器
│   ├─ listener/          # Hibernate 實體事件監聽
│   ├─ service/           # 共用商業邏輯，例如驗證、通用查詢
│   ├─ util/              # 工具類（日期、資料庫連線、雜湊等）
│   └─ vo/                # 系統常數、狀態列舉與通用 VO
│
├─ manager/               # 後台管理端（Admin / Operator）
│   ├─ event/             # 活動管理主模組
│   │   ├─ controller/    # 後台活動 API，例如建立、修改、狀態切換
│   │   ├─ dao/           # 活動資料存取
│   │   ├─ service/       # 活動商業邏輯（票種管理、關鍵字分類）
│   │   └─ vo/            # 活動相關資料物件
│   │
│   ├─ eventdetail/  ★    # 活動細節與報名人管理（我負責）
│   │   ├─ controller/    # 提供活動報名名單、票券統計等 API
│   │   ├─ dao/           # 報名人、票券與訂單資料存取
│   │   ├─ service/       # 後台報名管理邏輯（搜尋、匯出、統計）
│   │   └─ vo/            # 報名人資訊、訂單統計 VO
│   │
│   └─ member/            # 後台會員管理
│       ├─ controller/
│       ├─ dao/
│       ├─ service/
│       └─ vo/
│
└─ user/                  # 前台會員端（End-User）
    ├─ member/        ★   # 前台會員系統（我負責）
    │   ├─ controller/    # 會員註冊、登入、修改資料 API
    │   ├─ dao/           # 會員資料存取
    │   ├─ service/       **# 密碼雜湊、驗證信、登入快取（Redis）邏輯**
    │   └─ vo/            # 會員資料物件（User、Profile、AuthStatus）
    │
    └─ ticket/            # 前台票券查詢與購買
        ├─ controller/
        ├─ dao/
        ├─ service/
        └─ vo/
    - `application.properties` 為範例設定檔，非正式可發信的信箱設定。如需測試郵件功能，請聯絡專案成員或自行準備測試用信箱
2. **容器設定**
    - MySQL 與 Redis 均透過 Docker 容器建立，程式連線參數需與容器設定一致才能正常存取資料庫
