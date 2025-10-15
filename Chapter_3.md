# Chapter 3: Transport Layer - 傳輸層重點整理

本文件根據《Computer Networking: A Top-Down Approach, Eighth Edition》的 Chapter 3 投影片內容，整理出傳輸層的核心概念、主要協議（UDP 和 TCP）、可靠數據傳輸原理、流量控制和擁塞控制。

## 一、 章節核心重點整理 (Summary of Key Points)

### 1. 傳輸層服務與協定 (Transport Layer Services and Protocols)

* **邏輯通訊 (Logical Communication):** 傳輸層協定為**應用程式行程**（Application Processes）之間提供了邏輯通訊。
* **運作方式:**
    * **發送端 (Sender):** 將應用層訊息分解成**報文段 (segments)**，然後傳遞給網路層。
    * **接收端 (Receiver):** 將報文段重新組裝成訊息，傳遞給應用層。
* **網際網路主要傳輸協定 (Principal Internet Transport Protocols):**
    * **TCP (Transmission Control Protocol):** 連線導向、可靠、按序傳輸、擁塞控制、流量控制、需要連線建立（握手）。
    * **UDP (User Datagram Protocol):** 無連線、不可靠、無序傳輸，是對「盡力而為 (best-effort)」IP 的無附加功能擴展。
* **缺乏的服務:** 傳輸層協定不提供延遲或頻寬保證。

### 2. 多工與解多工 (Multiplexing and Demultiplexing)

* **多工 (Multiplexing)（發送端）:** 處理來自多個**套接字 (sockets)** 的數據，並添加傳輸層報頭，該報頭之後用於解多工。
* **解多工 (Demultiplexing)（接收端）:** 使用報頭資訊將接收到的報文段傳遞到正確的套接字。
* **解多工機制:**
    * **UDP（無連線）:** 僅使用報文段中的**目的埠號 (destination port #)** 進行解多工。具有相同目的埠號但不同源 IP 地址和/或源埠號的數據報將被導向同一套接字。
    * **TCP（連線導向）:** 使用**四元組**（源 IP 地址、源埠號、目的 IP 地址、目的埠號）來識別和導向報文段到適當的套接字。

### 3. 無連線傳輸：UDP

* **特點:** 「最簡化」的網際網路傳輸協定。
* **服務特性:** 「盡力而為」服務，報文段可能丟失或無序交付給應用層。
* **連線性:** 無連線，發送方和接收方之間無需握手（無 RTT 延遲），每個報文段獨立處理。
* **報頭:** 報頭尺寸小，無擁塞控制（發送方可盡可能快地發送）。
* **用途:** 串流媒體應用（容忍丟失，對速率敏感）、DNS、SNMP、HTTP/3 (QUIC)。
* **檢驗和 (Checksum):** 目的在於檢測傳輸報文段中的錯誤（翻轉位元）。UDP 報文段內容（包括報頭字段和 IP 地址）被視為 16 位元整數序列進行計算。

### 4. 可靠數據傳輸原理 (Principles of Reliable Data Transfer, RDT)

* **目標:** 在不可靠的底層通道上實現可靠的服務抽象。
* **RDT 協定發展 (rdt protocols):**
    * **rdt 1.0:** 在完全可靠通道上傳輸（無位元錯誤、無丟包）。
    * **rdt 2.0:** 在有**位元錯誤 (bit errors)** 的通道上傳輸。引入：**檢驗和 (Checksum)**、**確認 (ACKs) / 否定確認 (NAKs)**、**停等 (Stop-and-Wait)**。
    * **rdt 2.1 / rdt 2.2:** 處理 ACK/NAK 損壞或丟失：**序號 (Sequence Number)**，以及 **rdt 2.2 (NAK-free)**（發送對最後正確收到的數據包的 ACK）。
    * **rdt 3.0 (ARQ 協定):** 處理**錯誤和丟失**的通道。引入：**計時器 (Countdown Timer)**，超時重傳。
* **流水線機制 (Pipelining):** 允許發送方發送多個「in-flight」、未確認的數據包，以提高利用率。
    * **Go-Back-N (GBN):** 累積確認，超時時重傳超時數據包及其後續所有數據包。
    * **選擇性重複 (Selective Repeat, SR):** 單獨確認並緩衝正確接收的數據包，超時時僅重傳該單個數據包。

### 5. 連線導向傳輸：TCP

* **基本特性:** 點對點、可靠、按序**位元流**（無訊息邊界）、全雙工數據、連線導向、流量控制。
* **報文段結構 (Segment Structure):** 包含源埠號、目的埠號、序號、確認號、接收窗口 (rwnd)、檢驗和以及各種控制位（ACK, SYN, FIN 等）。
* **序號 (Sequence Numbers):** 報文段數據中**第一個位元組**在位元流中的「編號」。
* **確認號 (Acknowledgement Numbers):** 期望從另一方收到的**下一個位元組**的序號（累積確認）。
* **重傳 (Retransmission):**
    * **超時 (Timeout):** 超時重傳導致超時的報文段。
    * **快速重傳 (Fast Retransmit):** 如果發送方收到**三個額外**的相同數據的 ACK（**三重重複 ACK**），則立即重傳最小序號的未確認報文段。
* **RTT/超時間隔估計:** 使用**指數加權移動平均 (EWMA)** 計算 $\text{EstimatedRTT}$，並據此計算 $\text{TimeoutInterval} = \text{EstimatedRTT} + 4 \cdot \text{DevRTT}$。
* **流量控制 (Flow Control):** 接收方通過 TCP 報頭中的 **rwnd (接收窗口)** 字段來**公佈 (advertises)** 其空閒緩衝空間大小，發送方限制發送量不超過此值，以防止緩衝區溢出。
* **連線管理 (Connection Management):**
    * **三次握手 (3-way Handshake):** 客戶端和伺服器交換 SYN、SYNACK 和 ACK 報文段以建立連線。
    * **連線關閉:** 雙方都通過發送 FIN (FIN bit=1) 報文段來關閉這一側的連線。

### 7. 擁塞控制原理 (Principles of Congestion Control)

* **擁塞 (Congestion):** 「太多源發送太快，網路無法處理」。
* **表現:** 延遲過長（路由器緩衝區排隊）、數據包丟失（緩衝區溢出）。
* **擁塞成本 (Costs of Congestion):** 重傳導致的額外工作、不必要的重傳佔用鏈路容量、上游傳輸容量和緩衝的浪費。

### 8. TCP 擁塞控制 (TCP Congestion Control)

* **擁塞窗口 (cwnd):** 動態調整發送速率的限制變數。$\text{TCP Rate} \approx \text{cwnd} / \text{RTT}$。
* **AIMD (Additive Increase Multiplicative Decrease):**
    * **擁塞避免 (Congestion Avoidance):** 每經過一個 RTT，$\text{cwnd}$ 線性增加 1 MSS。
    * **乘性減少 (Multiplicative Decrease):** 在檢測到丟失事件時，$\text{cwnd}$ 減半。
* **慢啟動 (Slow Start):** 連線開始時，$\text{cwnd}$ 以指數級速度增加，直到達到 $\text{ssthresh}$ (慢啟動閾值)。
* **公平性 (Fairness):** 在理想假設下，TCP 通過 AIMD 機制實現了公平共享頻寬的目的。

### 9. 傳輸層功能演進：QUIC

* **QUIC (Quick UDP Internet Connections):** 運行在 **UDP 之上**的應用層協議（如 HTTP/3），具備自己的連線建立、錯誤控制和擁塞控制。
* **優勢:**
    * **單次握手 (1-handshake):** 比 TCP + TLS 的兩次串行握手更快建立連線狀態。
    * **無隊頭阻塞 (No Head-of-Line Blocking, HOL):** 由於多流設計，一個流中的錯誤不會阻塞其他流的數據交付。

---

## 三、 專有名詞解釋 (Glossary of Terminology)

| 術語 (Term) | 英文縮寫 (Abbreviation) | 解釋 (Explanation) |
| :--- | :--- | :--- |
| **報文段** | Segment | 傳輸層的數據單元。 |
| **套接字** | Socket | 應用程式進程與傳輸層之間的軟體接口。 |
| **多工** | Multiplexing | 發送端從多個套接字收集數據並封裝。 |
| **解多工** | Demultiplexing | 接收端將收到的報文段交付給正確的套接字。 |
| **傳輸控制協定** | TCP | 連線導向、可靠、全雙工的傳輸層協定。 |
| **使用者數據報協定** | UDP | 無連線、不可靠、「盡力而為」的傳輸層協定。 |
| **停等** | Stop-and-Wait | 發送一個數據包後必須等待確認才能發送下一個的協議。 |
| **累積確認** | Cumulative ACK | 確認號 N 表示接收方已正確接收序號直到（包括）N 的所有數據。 |
| **快速重傳** | Fast Retransmit | TCP 在收到**三個重複 ACK** 後立即重傳的機制。 |
| **接收窗口** | rwnd | TCP 報頭中的字段，指示接收方目前願意接受的位元組數（用於流量控制）。 |
| **流量控制** | Flow Control | 接收方控制發送方速率，防止接收緩衝區溢出。 |
| **擁塞控制** | Congestion Control | 控制發送方速率，防止網路（路由器）擁塞。 |
| **擁塞窗口** | cwnd | TCP 用於限制發送數據量的變數。 |
| **慢啟動** | Slow Start | TCP 連線建立初期，擁塞窗口指數級增加的階段。 |
| **擁塞避免** | Congestion Avoidance | 擁塞窗口線性（加法）增加的階段。 |
| **三次握手** | 3-way Handshake | TCP 連線建立過程。 |
| **QUIC** | Quick UDP Internet Connections | 在 UDP 之上的應用層多流傳輸協定。 |