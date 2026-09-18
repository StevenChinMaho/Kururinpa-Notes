# 網際網路與通訊協定導論（Internet & Communication Protocols）

> CSC521 網路通訊協定，吳俊興，國立高雄大學資訊工程學系

---

## 本節涵蓋主題

| 主題 | 說明 |
|---|---|
| 什麼是網際網路（Internet） | 由主機、通訊鏈路、路由器組成的「網路的網路」 |
| 通訊協定（Protocols）的定義 | 規範訊息格式、順序與動作 |
| TCP/IP 協定堆疊 | 五層架構：application、transport、network、link、physical |
| 封裝（Encapsulation） | 資料在各層加上標頭（Header）的過程 |
| TCP/IP 協定套件 | OSI 模型與 DARPA 分層的對照 |
| 相關協定 | DHCP、ARP、ICMP、NAT、Routing 之間的關係 |
| Ethernet Frame | 實際封包在資料鏈結層的封裝範例 |
| 4G EPS/SAE 架構 | 行動網路核心網元件（MME、S-GW、P-GW 等） |
| 5G SBA 架構 | 服務導向式核心網架構 |
| 3GPP CAPIF/SEAL/VAL 框架 | 垂直應用服務與 5G 核心網之間的介接架構 |

---

## 1. 什麼是網際網路（What's the Internet）

**Inter-networks（互聯網）**：連結全球數百萬台運算裝置的網路。

組成要素：

| 要素 | 說明 |
|---|---|
| Hosts（end systems） | 連上網路的終端裝置，執行 network apps |
| Communication links | 傳輸媒介，如光纖、銅線、無線電、衛星 |
| Routers | 轉送封包（packets，資料的分塊） |

**Internet** 定義為「network of networks」：

- 呈鬆散階層式（loosely hierarchical）結構
- 區分 public Internet（公眾網際網路）與 private intranet（私有網內網）
- 由 Internet 標準規範，包括：
  - **RFC**（Request For Comments）：正式技術文件
  - **IETF**（Internet Engineering Task Force）：制定標準的組織

### IETF 對 internet／Internet 的定義

| 來源 | internet（小寫） | Internet（大寫） |
|---|---|---|
| RFC 1983（1996） | 由路由器互連的網路集合 | 全球最大的 internet，為三層階層（backbone、mid-level、stub networks）組成的多協定網路 |
| RFC 4949（2007） | internetwork 的縮寫，即「網路的網路」 | 全球唯一、互連的商業、政府、教育等電腦網路系統，共用 IAB 訂定的協定套件與 ICANN 管理的名稱位址空間 |

> 「internet」泛指任何互連網路的集合，「Internet」專指全球唯一的那個公眾網際網路。

---

## 2. 通訊協定（Communication Protocols）的定義

協定（Protocol）規範三件事：

- **格式（Format）**：訊息的資料結構
- **順序（Order）**：訊息發送與接收的先後次序
- **動作（Actions）**：收發訊息時應採取的行為

---

## 3. TCP/IP 協定堆疊（五層模型）

| 層級 | 功能 | 代表協定 |
|---|---|---|
| Application | 支援網路應用程式 | HTTP、SMTP、FTP、DNS、DHCP |
| Transport | 主機間資料傳輸（host-host） | TCP、UDP |
| Network | 將 datagram 從來源路由到目的地 | IP、routing protocols |
| Link | 相鄰網路元件間的資料傳輸 | PPP、Ethernet |
| Physical | 位元在實體媒介上的傳送 | （對應各種實體媒介） |

---

## 4. 封裝（Encapsulation）

資料由上層往下層傳送時，每經過一層就會被加上該層的 Header，形成新的資料單元：

| 層級 | 資料單元名稱 | 結構 |
|---|---|---|
| Application | message | `M` |
| Transport | segment | `Ht \| M` |
| Network | datagram | `Hn \| Ht \| M` |
| Link | frame | `Hl \| Hn \| Ht \| M` |

流程：

1. **來源主機（source）**：資料從 application 層往下逐層加上 Header，最終以 frame 形式送出
2. **交換器（switch）**：僅處理到 link／physical 層，依 frame 轉送
3. **路由器（router）**：處理到 network 層，依 datagram 的位址進行路由後重新封裝為 frame 轉送
4. **目的主機（destination）**：收到 frame 後逐層拆開 Header，還原出原始 message

> 封裝的核心概念：每一層只認得自己的 Header，並將上層的內容視為「不透明」的資料（payload）往下傳遞。

---

## 5. TCP/IP 協定套件（OSI vs DARPA 對照）

| OSI 模型層級 | DARPA 分層 | 代表協定 |
|---|---|---|
| Application / Presentation / Session Layer | Application Layer | HTTP、FTP、SMTP、DNS、RIP、SNMP |
| Transport Layer | Transport Layer | TCP、UDP |
| Network Layer | Internet Layer | ARP、ICMP、IGMP、IP (IPv4)；IPv6、ICMPv6、ND、MLD |
| Data Link Layer / Physical Layer | Network Interface Layer | Ethernet、802.11 wireless LAN、Frame Relay、ATM |

> DARPA 分層將 OSI 七層簡化為四層，把 Session／Presentation／Application 合併為單一 Application Layer，並將 Data Link 與 Physical 合併為 Network Interface Layer。

---

## 6. 相關協定（Related Protocols）

| 元件 | 角色 | 涉及協定 |
|---|---|---|
| Host | 一般終端裝置 | TCP/UDP、ICMP、IP、ARP、Data link；透過 DHCP server 取得 IP address、Subnet、default router 等設定 |
| NAT Server | 位址轉換 | IP 與 NAT 模組互動，轉換內外部位址 |
| Router | 封包轉送與路由 | Routing protocols 產生 Routing table，供 IP 層查詢轉送路徑 |

- **DHCP（Dynamic Host Configuration Protocol）**：動態指派主機的 IP 位址等網路設定
- **ARP（Address Resolution Protocol）**：將 IP 位址對應到實體（MAC）位址
- **ICMP（Internet Control Message Protocol）**：傳遞錯誤與控制訊息
- **NAT（Network Address Translation）**：轉換內部私有位址與外部公開位址

---

## 7. Ethernet Frame 範例

資料由應用層往下封裝的具體流程：

1. Application Data（HTTP）先被切成合適大小的「chunks」
2. 加上 **TCP Segment Header**，標示目的的 port／process
3. 加上 **IP Datagram Header**，標示目的主機
4. 加上 **Ethernet Header**，標示區域網路下一躍點（next hop）的位址

實際 Ethernet_II TCP/IP 封包結構：

| Preamble | Dest Address | Source Address | Type | IP Header | TCP Header | Data | FCS |
|---|---|---|---|---|---|---|---|

- **Ethernet DLC Header**：Preamble～Type
- **IP Datagram**：IP Header～Data
- **DLC Trailer**：FCS（Frame Check Sequence，用於錯誤檢查）

---

## 8. 3GPP 4G - EPS（Evolved Packet System）/ SAE

**EPC（Evolved Packet Core）** 為 EPS 的核心組成，主要網元：

| 網元 | 全名 | 功能 |
|---|---|---|
| MME | Mobility Management Entity | LTE 的關鍵控制節點，負責 UE paging；在 attach／handover 時選擇 S-GW；並透過 HSS 進行使用者認證 |
| S-GW | Serving Gateway | 管理與儲存 UE context，負責路由與轉送使用者資料封包 |
| P-GW | PDN Gateway | 提供 UE 與外部封包資料網路（PDN）之間的連線 |
| ePDG | Evolved Packet Data Gateway | 保護 UE 透過不受信任的 non-3GPP 存取連到 EPC 時的資料傳輸安全 |
| ANDSF | Access Network Discovery and Selection Function | 提供 UE 探索可用存取網路（3GPP 或 non-3GPP）的資訊 |
| HSS | Home Subscriber Server | 儲存用戶資料，供 MME 進行認證 |

- UE 可透過 **3GPP Access Technology**（經 eNodeB）或 **Non-3GPP Access Technology**（經 Wi-Fi AP）連上 EPC
- 兩種路徑都必須通過紅線所示的邊界節點才能進入 Evolved Packet Core

---

## 9. 5G Service-Based Architecture（SBA）

5G 核心網以「服務」為單位設計，各網路功能（NF）透過 **Service-Based Interface（SBI）** 互相溝通：

| 網路功能 | 全名 | 對應服務介面 |
|---|---|---|
| NSSF | Network Slice Selection Function | Nnssf |
| NEF | Network Exposure Function | Nnef |
| NRF | Network Repository Function | Nnrf |
| UDM | Unified Data Management | Nudm |
| PCF | Policy Control Function | Npcf |
| AF | Application Function | Naf |
| AUSF | AUthentication Server Function | Nausf |
| AMF | Access and Mobility Management Function | Namf |
| SMF | Session Management Function | Nsmf |

資料平面路徑：`UE — (R)AN — UPF（User Plane Function）— DN（Data Network）`

- N1：UE 與 AMF 之間的控制訊令
- N2：(R)AN 與 AMF 之間
- N3：(R)AN 與 UPF 之間（使用者資料）
- N4：SMF 與 UPF 之間
- N5：PCF 與 AF 之間
- N6：UPF 與 DN 之間
- N9：UPF 之間（可有多個 UPF 串接）

> SBA 與 4G EPC 最大差異在於：4G 各網元間多為點對點介面，5G 則統一透過 SBI 以服務化方式互相呼叫。

---

## 10. 3GPP CAPIF/SEAL/VAL Framework

此框架定義垂直應用（Vertical Application，如 V2X、UAS、工廠自動化）如何透過標準化介面存取 5G 核心網能力：

| 層級 | 全名 | 說明 |
|---|---|---|
| VAL | Vertical Application enabler Layer | 提供特定垂直領域應用的能力（如 V2X 應用邏輯） |
| SEAL | Service Enabler Architecture Layer | 提供跨垂直領域共用的服務賦能能力 |
| CAPIF | Common API Framework | 提供北向（Northbound）API 的統一管理與開放 |

架構關係：

- **UE 端**：Vertical Application Client(s) → VAL → SEAL → UE，經 5G AN（gNB）連上 5GC
- **網路端**：Vertical Application Server(s) 透過 CAPIF APIs 存取 Common API Framework，再經 Northbound APIs 存取 AMF/SMF、NSSF/NEF/NRF/PCF/AUSF/UDM、AF/NF 等 5GC 內部功能，最終經 UPF、SBI 連到 DN

---

## 重點整理

| 概念 / 協定 | 功能 | 關鍵重點 |
|---|---|---|
| Internet | 全球唯一、由路由器互連的「網路的網路」 | 與泛稱的 internet 不同，需共用 IAB 協定套件與 ICANN 位址空間 |
| Protocol | 規範訊息格式、順序、動作 | 三要素缺一不可 |
| TCP/IP 五層 | Application/Transport/Network/Link/Physical | 對應 message／segment／datagram／frame |
| Encapsulation | 逐層加上 Header 再往下傳 | Router 只拆到 network 層，Switch 只拆到 link 層 |
| DHCP/ARP/ICMP/NAT | Host、NAT Server、Router 三者的輔助協定 | DHCP 配位址、ARP 解析 MAC、ICMP 傳錯誤訊息、NAT 轉換位址 |
| EPC（4G） | MME/S-GW/P-GW/ePDG/ANDSF | 點對點介面為主，MME 為控制核心 |
| SBA（5G） | AMF/SMF/UPF + 各項 xxF | 服務化介面（SBI），以 Nxxx 命名各服務 |
| CAPIF/SEAL/VAL | 垂直應用存取 5G 核心網的分層框架 | VAL 貼近應用、SEAL 提供共用能力、CAPIF 統一開放 API |
