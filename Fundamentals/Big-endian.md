---
title: 位元組順序 (Endianness / Big-endian & Little-endian)
tags: [Fundamentals, Computer-Architecture, Memory]
date: 2026-07-01
aliases: [Big-endian, Little-endian, Endianness, 位元組順序, 大端, 小端]
---

# 位元組順序 (Endianness)

> [!abstract] **一句話**
> 當一個多位元組的數值（例如 32-bit 整數）存進記憶體時,「哪個 byte 放在低位址」有兩種相反約定——這就是位元組順序 (Endianness)。搞錯會讓跨平台、跨網路傳輸的資料整個亂掉。

## 1. 兩種約定

以 32-bit 整數 `0x12345678` 存入位址 `0x100` 起始的記憶體為例（`0x12` 是最高有效位元組 MSB,`0x78` 是最低有效位元組 LSB）:

| 位址 | Big-endian (大端) | Little-endian (小端) |
|---|---|---|
| `0x100` | `0x12` (MSB 先) | `0x78` (LSB 先) |
| `0x101` | `0x34` | `0x56` |
| `0x102` | `0x56` | `0x34` |
| `0x103` | `0x78` (LSB 最後) | `0x12` (MSB 最後) |

- **Big-endian (大端)**:最高有效位元組放在**最低**位址。「符合人類閱讀順序」——由左（高位）到右（低位）。網路傳輸標準 (network byte order) 就是大端。
- **Little-endian (小端)**:最低有效位元組放在**最低**位址。x86/x64、多數 ARM 採用。優點:低位元組位址固定,型別轉換（如 32-bit 讀成 8-bit）不需調整位址。

## 2. 為什麼要在意

> [!danger] **典型踩雷場景**
> - **網路通訊**:發送端小端、接收端大端,不做轉換 (`htonl`/`ntohl`) 就會收到亂數。
> - **檔案格式 / 二進位序列化**:同一份 `.bin` 在不同架構機器上讀出不同值。
> - **DSP / 嵌入式**:許多處理器（如 [[DCS/TMS320C6000/核心架構與Pipeline|TMS320C6000]]）可切換大小端模式,設定錯誤會讓載入的常數表、資料全錯。

## 3. 判斷與轉換

- **判斷本機**:寫一個 `int = 1`,用 `char*` 讀第一個 byte——是 `1` 就是小端,是 `0` 就是大端。
- **轉換**:C 的 `htons/htonl`（host to network）、`ntohs/ntohl`(network to host),或編譯器內建 `__builtin_bswap32` 之類的 byte-swap 指令。

## 4. 一個記憶法

「**大端大在前**」——Big-endian 把 big（最高位）那端放在前面（低位址）。反之小端把小的那端放前面。

---
**相關筆記**：[[DCS/TMS320C6000/Memory_Map與EMIF|DSP 記憶體與資料傳輸]] · [[Fundamentals/NVMe_SSD|NVMe SSD]] · [[index|🌐 全域索引]]
