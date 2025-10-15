# Chapter 2 – Application Layer 筆記整理

> 根據 *Computer Networking: A Top-Down Approach (8th Edition, Kurose & Ross)* 整理  
> 含重點摘要、專有名詞解釋與補充說明

---

## 📚 目錄 (Table of Contents)

1. [應用層概念與架構](#一-應用層概念與架構)
2. [Web 與 HTTP](#二-web-與-http)
3. [電子郵件系統 (E-mail)](#三-電子郵件系統-e-mail)
4. [DNS 網域名稱系統](#四-dns-網域名稱系統)
5. [P2P 對等式架構](#五-p2p-對等式架構)
6. [影音串流與 CDN](#六-影音串流與-cdn)
7. [Socket Programming](#七-socket-programming)
8. [章節總結](#八-章節總結)

---

## 一、應用層概念與架構

### 📌 重點整理
- **應用層 (Application Layer)**：提供應用程式間通訊的協定與規範。
- 程式僅需在端系統上運作，不需撰寫網路核心設備程式。
- **Socket** 是應用層與傳輸層的介面，負責資料傳遞。

### 🧠 應用架構類型
1. **Client-Server 架構**
   - Server 永久在線，具有固定 IP。
   - Client 負責發起連線與請求。
   - 範例：HTTP、FTP、IMAP。
2. **Peer-to-Peer (P2P) 架構**
   - 節點互相通訊，無固定伺服器。
   - 可自我擴展，但管理複雜。

### 🔍 專有名詞
| 名詞 | 解釋 |
|------|------|
| Process | 執行中的程式。 |
| Socket | 應用層與傳輸層間的界面。 |
| Port number | 識別同一主機上不同應用程式。 |

### 💡 補充
- **TCP**：可靠、有序、面向連線。
- **UDP**：不可靠、無連線。
- 常見應用層協定：HTTP、SMTP、DNS、BitTorrent、Zoom。

---

## 二、Web 與 HTTP

### 📌 重點整理
- **HTTP (HyperText Transfer Protocol, 超文字傳輸協定)** 是 Web 的應用層協定。
- **Client**：瀏覽器，負責發送請求並顯示結果。
- **Server**：網頁伺服器（如 Apache）。
- 一個網頁由多個 **objects** 組成，每個物件用 URL 指定。

### 🔍 HTTP 連線方式
1. **Non-persistent (HTTP/1.0)**：每次請求都需重新開 TCP 連線。
2. **Persistent (HTTP/1.1)**：可在同一連線中傳多個物件。

### 🔍 HTTP 方法
| 方法 | 用途 |
|------|------|
| GET | 取得伺服器上的資源。 |
| POST | 傳送表單或資料。 |
| HEAD | 只取 header，不取資料。 |
| PUT | 上傳新檔案或覆蓋舊檔。 |

### 🔍 狀態碼 (Status Codes)
| 狀態碼 | 意義 |
|--------|------|
| 200 OK | 成功 |
| 301 Moved Permanently | 網址搬遷 |
| 400 Bad Request | 錯誤請求 |
| 404 Not Found | 找不到資源 |
| 505 HTTP Version Not Supported | 不支援版本 |

### 🍪 Cookies
- 讓伺服器能記住使用者（HTTP 本身是 stateless）。
- 四個組成：
  1. 伺服器回應中的 `Set-Cookie`。
  2. 後續請求中的 `Cookie`。
  3. 瀏覽器的 cookie 檔案。
  4. 伺服器端的使用者資料庫。

### 💡 Web Cache / Proxy
- 快取伺服器可降低延遲與流量。
- **Conditional GET**：若快取未更新則回傳 304 Not Modified。

### ⚡ HTTP/2 與 HTTP/3
- **HTTP/2**：分 frame 傳輸、支援多工與優先權。
- **HTTP/3**：以 **UDP + QUIC** 實作，更快更安全。

---

## 三、電子郵件系統 (E-mail)

### 📌 三大元件
1. **User Agent (UA)**：郵件軟體，如 Outlook。
2. **Mail Server**：收發郵件的伺服器。
3. **SMTP (Simple Mail Transfer Protocol)**：使用 TCP port 25 傳送郵件。

### 📮 郵件傳遞流程
1. 使用者 → 寄信伺服器（SMTP）  
2. 寄信伺服器 → 收信伺服器（SMTP）  
3. 收信人透過 **IMAP 或 HTTP** 收取信件

### 🔍 Mail Access Protocol
| 協定 | 說明 |
|------|------|
| IMAP | 郵件保留於伺服器，可建立資料夾。 |
| POP3 | 下載郵件後刪除伺服器副本。 |
| HTTP Mail | Web 郵件（如 Gmail）。 |

### 💬 SMTP 範例
```
C: HELO crepes.fr
S: 250 Hello
C: MAIL FROM: <alice@crepes.fr>
S: 250 OK
C: RCPT TO: <bob@hamburger.edu>
S: 250 OK
C: DATA
S: 354 End with '.'
C: Hello Bob.
C: .
S: 250 Message accepted
```

---

## 四、DNS 網域名稱系統

### 📌 重點
- DNS（Domain Name System）用來把主機名稱轉換為 IP 位址。
- 是 **分散式階層式資料庫**。

### 🧱 層級架構
1. Root Servers  
2. TLD Servers (.com, .org, .edu...)  
3. Authoritative Servers  
4. Local DNS Server（每個 ISP 都有）

### 🔍 查詢方式
- **Iterative**：伺服器回「去問下一個」。
- **Recursive**：上層伺服器幫你一路查到底。

### 🔍 Resource Record (RR)
| Type | 說明 |
|------|------|
| A | hostname → IP |
| NS | domain → 該域權威伺服器 |
| CNAME | 別名（alias）→ 真實名稱 |
| MX | domain → Mail Server 名稱 |

### 💡 補充
- 不集中化是為了避免單點故障與流量過大。
- 本地 DNS 通常會快取結果以提升效率。

---

## 五、P2P 對等式架構

### 📌 重點整理
- 節點直接通訊，無固定伺服器。
- 每個 peer 同時上傳與下載資料。

### 📊 傳輸效率比較
| 模式 | 特性 |
|------|------|
| Client-Server | 伺服器需上傳 N 份檔案，效率隨 N 增長。 |
| P2P | 各節點共享頻寬，效率更高。 |

### 💾 BitTorrent
- 檔案被切為 **256KB chunks**。
- **Tracker**：追蹤活躍 peers。
- **Rarest first**：優先下載稀有區塊。
- **Tit-for-tat**：互惠上傳策略。

---

## 六、影音串流與 CDN

### 📌 重點
- 串流影音佔網路流量約 80%。
- 挑戰：頻寬不穩、延遲、使用者互動需求。

### 🔍 DASH (Dynamic Adaptive Streaming over HTTP)
- 影片分段、每段多種畫質版本。
- Client 根據頻寬動態選取。
- **Manifest file** 指示可用段落與 URL。

### 🌎 CDN (Content Distribution Network)
- 透過多地部署伺服器讓使用者就近存取。
- 策略：
  - **Enter deep**（如 Akamai）：深入接入網路。
  - **Bring home**（如 Limelight）：集中於主要節點。
- 例：Netflix 影片放於 Amazon Cloud + 自家 CDN。

---

## 七、Socket Programming

### 📌 UDP Socket
- 無連線、不保證順序。
```python
# UDP client example
clientSocket.sendto(message.encode(), (serverName, 12000))
```

### 📌 TCP Socket
- 有連線、可靠。
```python
# TCP server example
connectionSocket, addr = serverSocket.accept()
sentence = connectionSocket.recv(1024)
```

---

## 八、章節總結

### 🧩 關鍵觀念
| 主題 | 概念 |
|------|------|
| 架構 | Client-Server / P2P |
| 協定特性 | 狀態、有無連線、集中 vs 分散 |
| 傳輸需求 | 可靠性、頻寬、延遲 |
| 協定範例 | HTTP, SMTP, DNS, BitTorrent, DASH |
| 程式介面 | Socket API（TCP/UDP） |

### 💡 核心精神
- **Complexity at the edge**：讓核心簡單，把複雜功能留在端點。  
- 不同應用層協定都在平衡：
  - 可靠性 vs. 延遲  
  - 集中 vs. 分散  
  - 狀態化 vs. 無狀態

---

> 完整筆記 by ChatGPT｜Based on Kurose & Ross, *Computer Networking: A Top-Down Approach (8th ed.)*
