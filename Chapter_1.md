# Chapter I: Introduction - 電腦網路導論重點整理

本文件根據《Computer Networking: A Top-Down Approach, SEVENTH EDITION, Global Edition》的 Chapter 1 投影片內容，整理出核心概念、額外補充，並解釋了關鍵專有名詞。

## 一、 章節核心重點整理 (Summary of Key Points)

本章節介紹了電腦網路的基本概念，主要圍繞「網際網路 (Internet)」的運作方式及其組成結構。

### 1. 網際網路的「螺絲釘」觀點 (Nuts and Bolts View)

* **端系統 (End Systems) / 主機 (Hosts):** 數十億連接的計算設備，例如 PC、伺服器、無線筆記本電腦等。
* **通訊鏈路 (Communication Links):** 連接各設備的介質，包括光纖、銅線、無線電、衛星等。
    * 傳輸速率（Transmission Rate）也稱為頻寬（Bandwidth）。
* **分組交換機 (Packet Switches):** 負責轉發數據塊（packets）的設備，主要指路由器 (Routers) 和交換機 (Switches)。
* **網路的網路 (Network of Networks):** 網際網路由互連的 ISP (Internet Service Providers) 組成。

### 2. 協定 (Protocol)

* **定義:** 協定定義了網路實體之間發送和接收訊息的格式、順序，以及在訊息傳輸或接收時採取的動作。
* **重要性:** 網際網路中的所有通訊活動都受協定規範。常見協定包括 TCP, IP, HTTP, Skype, 802.11。
    * 網際網路標準由 RFC (Request for comments) 文件定義，並由 IETF (Internet Engineering Task Force) 制定。

### 3. 網路的結構 (Network Structure)

網路結構可分為三個主要部分：網路邊緣、接入網路和網路核心。

| 部分 | 組成要素 | 功能簡述 |
| :--- | :--- | :--- |
| **網路邊緣** (Network Edge) | 主機（客戶端和伺服器）、接入網路、實體媒體 | 應用程式運行的場所，並提供連接到核心網路的通道。 |
| **接入網路** (Access Networks) | 居家網路（DSL/Cable Modem）、企業網路（Ethernet）、無線網路（WiFi/3G/4G/LTE） | 連接端系統到核心網路。 |
| **網路核心** (Network Core) | 互連的路由器（Router Mesh）、網路的網路 | 負責在來源與目的地之間轉發數據包。 |

### 4. 核心網路的兩種切換技術 (Network Core Switching)

| 技術 | 特點 | 應用場景 |
| :--- | :--- | :--- |
| **分組交換** (Packet Switching) | 訊息被分成數據包 (packets)，每個數據包以滿鏈路容量 (full link capacity) 傳輸。允許資源共享，但可能造成擁塞、延遲和丟包。 | 適用於突發性 (bursty) 數據。 |
| **電路交換** (Circuit Switching) | 為「呼叫」（call）預先分配和保留端到端資源。資源是**專用的，沒有共享**，性能有保證 (guaranteed)。例如使用 FDM 或 TDM 分配頻率或時間槽。 | 傳統電話網路。 |

### 5. 網路性能指標 (Network Performance)

* **延遲 (Delay):** 數據包從一端傳輸到另一端所需的時間。節點延遲 ($d_{nodal}$) 由四個部分組成:
    * **節點處理延遲 ($d_{proc}$):** 檢查位元錯誤、確定輸出鏈路。通常 $< msec$。
    * **排隊延遲 ($d_{queue}$):** 在輸出鏈路等待傳輸的時間，取決於路由器擁塞程度。
    * **傳輸延遲 ($d_{trans}$):** 將 L 位元數據包「推入」速率為 R 的鏈路所需的時間。公式：$d_{trans} = L/R$。
    * **傳播延遲 ($d_{prop}$):** 位元在物理鏈路上傳播所需的時間。公式：$d_{prop} = d/s$ (d: 物理鏈路長度; s: 傳播速度 $\approx 2 \times 10^8~m/sec$)。
* **丟包 (Loss):** 數據包到達一個已滿的緩衝區（queue/buffer）時會被丟棄（丟失）。
* **吞吐量 (Throughput):** 位元在發送方和接收方之間傳輸的速率 (bits/time unit)。
    * 平均端到端吞吐量受路徑上傳輸速率最小的**瓶頸鏈路 (bottleneck link)** 限制。

### 6. 協定層和服務模型 (Protocol Layers)

* **分層模型:** 將網路功能分為層次，每層實現一個服務，並依賴下一層提供的服務。
* **網際網路協定堆疊 (Internet Protocol Stack)**:
    1.  **應用層 (Application):** 支援網路應用程式 (e.g., FTP, SMTP, HTTP)。
    2.  **傳輸層 (Transport):** 處理行程-行程 (process-process) 數據傳輸 (e.g., TCP, UDP)。
    3.  **網路層 (Network):** 路由數據報 (datagrams) 從來源到目的地 (e.g., IP, routing protocols)。
    4.  **鏈路層 (Link):** 鄰近網路元素之間的數據傳輸 (e.g., Ethernet, 802.11 (WiFi), PPP)。
    5.  **實體層 (Physical):** 位元「在線路上」傳輸。
* **OSI 模型:** 除了上述五層外，OSI 參考模型還包括**表現層 (Presentation)** 和**會話層 (Session)**。這些服務在網際網路堆疊中，如果需要，必須由應用層實現。
* **封裝 (Encapsulation):** 在發送方，每一層都會在來自上層的數據（訊息、片段等）前添加自己的標頭 ($H_t, H_n, H_l$)，形成新的數據單元（如 Segment, Datagram, Frame）。

### 7. 網路安全 (Network Security)

網際網路最初的設計並未考慮太多安全性。

* **惡意軟體 (Malware):**
    * **病毒 (Virus):** 透過接收/執行對象（如電子郵件附件）進行自我複製感染。
    * **蠕蟲 (Worm):** 透過被動接收對象使其自我執行，從而進行自我複製感染。
    * **間諜軟體 (Spyware):** 可以記錄按鍵、瀏覽的網站，並將資訊上傳到收集站點。
* **拒絕服務攻擊 (Denial of Service, DoS):** 攻擊者透過大量虛假流量壓垮資源（伺服器、頻寬），使其無法為合法流量服務。
* **分組嗅探 (Packet Sniffing):** 在廣播媒體上，網路介面以**混雜模式 (promiscuous)** 讀取/記錄所有經過的數據包（可能包括密碼）。
* **IP 欺騙 (IP Spoofing):** 發送帶有虛假源地址的數據包。

---

## 二、 額外補充與說明 (Further Elaboration)

### 1. 接入網路的實體媒體細節 (Physical Media Details)

* **有導向媒體 (Guided Media):** 訊號在固體介質中傳播。包括雙絞線 (TP)、同軸電纜 和光纖 (Fiber Optic Cable)。光纖因使用光脈衝，具有高速度（10s-100s Gbps） 和低錯誤率，且對電磁雜訊免疫。
* **無線電鏈路 (Radio Link Types):** 訊號在電磁頻譜中傳播。包括地面微波、LAN (WiFi)、廣域網 (Wide-Area/Cellular) (e.g., 4G: LTE) 和衛星鏈路。

### 2. 網路核心結構的演變 (Evolution of Network Core Structure)

網際網路結構是一個「網路的網路」。由於直接連接每個接入 ISP ($O(N^2)$ 連接) 成本過高且無法擴展，因此演變出層次化的結構:

* **Tier 1 ISP:** 數量較少，是全球性的商業 ISP (e.g., Level 3, Sprint, AT&T, NTT)。
* **區域 ISP (Regional ISP):** 連接接入網路到 Tier-1 ISP。
* **網際網路交換點 (IXP):** 允許多個 ISP 在此透過**對等互聯 (peering link)** 交換流量。
* **內容提供商網路 (Content Provider Network):** 大型內容提供商（如 Google）運行自己的私人網路，通常繞過 Tier-1 和區域 ISP，將服務和內容更接近終端用戶。

### 3. 隊列延遲與流量強度 (Queueing Delay and Traffic Intensity)

平均排隊延遲與**流量強度** ($\text{Traffic Intensity} = La/R$) 密切相關。

* $L$: 數據包長度 (bits)
* $a$: 平均數據包到達速率
* $R$: 鏈路頻寬 (bps)

當 $La/R \sim 0$ 時，平均排隊延遲很小；當 $La/R \to 1$ 時，延遲會變得非常大。若 $La/R > 1$，則平均延遲將趨於無限，因為到達的工作量大於可服務的工作量。

---

## 三、 專有名詞解釋 (Glossary of Terminology)

| 術語 (Term) | 英文縮寫 (Abbreviation) | 解釋 (Explanation) |
| :--- | :--- | :--- |
| **主機** | Host / End System | 運行網路應用程式、連接到網路邊緣的計算設備（如 PC, 伺服器, 智慧型手機）。 |
| **頻寬** | Bandwidth | 通訊鏈路的傳輸速率 (bits/sec)，又稱鏈路容量 (link capacity)。 |
| **數據包** | Packet | 應用層訊息被拆分成的較小的數據塊（chunks of data），在網路中傳輸。 |
| **路由器** | Router | 核心網路中的分組交換機，負責根據目標地址進行路由和轉發。 |
| **協定** | Protocol | 規範網路實體之間所有通訊活動的規則，定義了訊息的格式、順序和動作。 |
| **傳輸延遲** | $d_{trans}$ | 將 L-bit 數據包完全推入速率為 R 的鏈路所需的時間，等於 $L/R$。 |
| **傳播延遲** | $d_{prop}$ | 位元在物理鏈路上傳輸（傳播）所需的時間，等於 $d/s$。 |
| **排隊延遲** | $d_{queue}$ | 數據包在路由器緩衝區中等待被傳輸到輸出鏈路所需的時間，與擁塞程度相關。 |
| **流量強度** | Traffic Intensity | 用於衡量鏈路擁塞程度的指標，等於 $La/R$。 |
| **瓶頸鏈路** | Bottleneck Link | 限制端到端吞吐量的鏈路，其傳輸速率決定了整體吞吐量的最大值。 |
| **路由** | Routing | 確定數據包在源-目的之間所走的路徑。 |
| **轉發** | Forwarding | 將數據包從路由器的輸入端口移動到適當的輸出端口。 |
| **DoS** | Denial of Service | 拒絕服務攻擊，透過大量虛假流量使目標資源對合法使用者不可用。 |
| **IP 欺騙** | IP Spoofing | 發送帶有虛假（偽造）源地址的數據包。 |
| **traceroute** | - | 一個程式，用於測量從源主機到目標路徑上每個路由器的延遲。 |
| **IETF** | Internet Engineering Task Force | 網際網路工程任務組，負責制定網際網路標準。 |
| **RFC** | Request for Comments | 記錄網際網路標準、協定、方法和研究的文獻。 |
| **IXP** | Internet Exchange Point | 網際網路交換點，是不同 ISP 之間進行對等互聯 (peering) 和交換流量的地方。 |