# Dokumen Uji Kasus & Matriks Validasi (Test Cases Matrix)
## Day 30 Banking Reconciliation Enterprise REFramework

Dokumen ini memuat skenario pengujian komprehensif, matriks validasi untuk seluruh 25 transaksi perbankan, hasil verifikasi Tugas Praktikum 1, 2, dan 3, serta klasifikasi status pengujian sistem.

---

## 1. Klasifikasi Status Pengujian (Test Status Legend)

Sesuai ketentuan standar QA & Handbook Praktikum:
- **`TEST PASSED`**: Telah diuji dan diverifikasi secara aktual, seluruh parameter logika, transisi state machine, dan data output cocok 100%.
- **`TEST FAILED`**: Pengujian menghasilkan anomali, crash yang tidak tertangani, atau data mismatch.
- **`NOT TESTED`**: Skenario belum dieksekusi.
- **`REQUIRES ORCHESTRATOR CREDENTIALS`**: Skenario memerlukan login interaktif UiPath Automation Cloud aktif dari pengguna.
- **`REQUIRES MANUAL UI INTERACTION`**: Skenario yang memerlukan interaksi antarmuka pengguna manusia secara visual di desktop.

---

## 2. Matriks Pengujian 25 Transaksi (Reconciliation Test Matrix)

Berikut adalah matriks hasil rekonsiliasi tiga arah (*3-Way Matching*: Core Banking vs Switching Gateway vs Banking Monitoring Web Portal):

| No | ID Transaksi | No Rekening | Nominal Core | Nominal Switch | Nominal Portal | Status Core | Status Switch | Status Portal | Kategori | Alasan Audit / Keterangan | Status Verifikasi |
| :---: | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :--- | :---: |
| 1 | `TRX-2026-0001` | 1001234567 | Rp1.500.000 | Rp1.500.000 | Rp1.500.000 | SUCCESS | SUCCESS | SUCCESS | `Audit_Clean` | Transaksi Cocok Sempurna (3-Way Matched) | **TEST PASSED** |
| 2 | `TRX-2026-0002` | 2002345678 | Rp250.000 | Rp250.000 | Rp250.000 | SUCCESS | SUCCESS | SUCCESS | `Audit_Clean` | Transaksi Cocok Sempurna (3-Way Matched) | **TEST PASSED** |
| 3 | `TRX-2026-0003` | 3003456789 | Rp750.000 | Rp750.000 | Rp750.000 | SUCCESS | SUCCESS | SUCCESS | `Audit_Clean` | Transaksi Cocok Sempurna (3-Way Matched) | **TEST PASSED** |
| 4 | `TRX-2026-0004` | 4004567890 | Rp5.000.000 | Rp4.950.000 | Rp5.000.000 | SUCCESS | SUCCESS | SUCCESS | `Audit_Exceptions` | **BRE**: Variance Amount (Core: 5.000.000 vs Switch: 4.950.000) | **TEST PASSED** |
| 5 | `TRX-2026-0005` | 12345 | Rp1.000.000 | Rp1.000.000 | Rp1.000.000 | SUCCESS | SUCCESS | SUCCESS | `Audit_Exceptions` | **BRE**: Invalid account format: minimal 8 digit numerik (Panjang: 5 digit) | **TEST PASSED** |
| 6 | `TRX-2026-0006` | 6006789012 | Rp2.000.000 | Rp2.000.000 | Rp2.000.000 | SUCCESS | FAILED | SUCCESS | `Audit_Exceptions` | **BRE**: Variance Status (Core: SUCCESS vs Switch: FAILED) | **TEST PASSED** |
| 7 | `TRX-2026-0007` | 7007890123 | Rp3.000.000 | Rp3.000.000 | Rp3.000.000 | SUCCESS | SUCCESS | SUCCESS | `Audit_Clean` | Transaksi Cocok Sempurna (Disertai Uji Crash & Auto-Retry) | **TEST PASSED** |
| 8 | `TRX-2026-0008` | 8008901234 | Rp120.000 | Rp120.000 | Rp120.000 | SUCCESS | SUCCESS | SUCCESS | `Audit_Clean` | Transaksi Cocok Sempurna (3-Way Matched) | **TEST PASSED** |
| 9 | `TRX-2026-0009` | 9009012345 | Rp850.000 | Rp850.000 | Rp850.000 | SUCCESS | SUCCESS | SUCCESS | `Audit_Clean` | Transaksi Cocok Sempurna (3-Way Matched) | **TEST PASSED** |
| 10 | `TRX-2026-0010` | 1000123456 | Rp500.000 | Rp0 | Rp500.000 | SUCCESS | NOT_FOUND | SUCCESS | `Audit_Exceptions` | **BRE**: NOT FOUND IN SWITCH (Transaksi tidak ditemukan di Switching Gateway) | **TEST PASSED** |
| 11 | `TRX-2026-0011` | 1101234567 | Rp600.000 | Rp600.000 | Rp600.000 | SUCCESS | SUCCESS | SUCCESS | `Audit_Clean` | Transaksi Cocok Sempurna (3-Way Matched) | **TEST PASSED** |
| 12 | `TRX-2026-0012` | 1202345678 | Rp1.800.000 | Rp1.800.000 | Rp1.800.000 | SUCCESS | SUCCESS | SUCCESS | `Audit_Clean` | Transaksi Cocok Sempurna (3-Way Matched) | **TEST PASSED** |
| 13 | `TRX-2026-0013` | 1303456789 | Rp0 | Rp0 | Rp0 | SUCCESS | SUCCESS | SUCCESS | `Audit_Exceptions` | **BRE**: Zero or negative amount: Nominal Core harus > 0 (Ditemukan: Rp0) | **TEST PASSED** |
| 14 | `TRX-2026-0014` | 1404567890 | Rp990.000 | Rp900.000 | Rp990.000 | SUCCESS | SUCCESS | SUCCESS | `Audit_Exceptions` | **BRE**: Variance Amount (Core: 990.000 vs Switch: 900.000) | **TEST PASSED** |
| 15 | `TRX-2026-0015` | 1505678901 | Rp350.000 | Rp350.000 | Rp350.000 | SUCCESS | SUCCESS | SUCCESS | `Audit_Clean` | Transaksi Cocok Sempurna (3-Way Matched) | **TEST PASSED** |
| 16 | `TRX-2026-0016` | 1606789012 | Rp2.500.000 | Rp2.500.000 | Rp2.500.000 | SUCCESS | TIMEOUT | SUCCESS | `Audit_Exceptions` | **BRE**: Variance Status (Core: SUCCESS vs Switch: TIMEOUT) | **TEST PASSED** |
| 17 | `TRX-2026-0017` | 1707890123 | Rp450.000 | Rp450.000 | Rp450.000 | SUCCESS | SUCCESS | SUCCESS | `Audit_Clean` | Transaksi Cocok Sempurna (3-Way Matched) | **TEST PASSED** |
| 18 | `TRX-2026-0018` | 9988 | Rp1.100.000 | Rp1.100.000 | Rp1.100.000 | SUCCESS | SUCCESS | SUCCESS | `Audit_Exceptions` | **BRE**: Invalid account format: minimal 8 digit numerik (Panjang: 4 digit) | **TEST PASSED** |
| 19 | `TRX-2026-0019` | 1909012345 | Rp2.100.000 | Rp2.100.000 | Rp2.100.000 | SUCCESS | SUCCESS | SUCCESS | `Audit_Clean` | Transaksi Cocok Sempurna (3-Way Matched) | **TEST PASSED** |
| 20 | `TRX-2026-0020` | 2000123456 | Rp175.000 | Rp175.000 | Rp175.000 | SUCCESS | SUCCESS | SUCCESS | `Audit_Clean` | Transaksi Cocok Sempurna (3-Way Matched) | **TEST PASSED** |
| 21 | `TRX-2026-0021` | 2101234567 | Rp920.000 | Rp920.000 | Rp920.000 | SUCCESS | SUCCESS | SUCCESS | `Audit_Clean` | Transaksi Cocok Sempurna (3-Way Matched) | **TEST PASSED** |
| 22 | `TRX-2026-0022` | 2202345678 | Rp4.500.000 | Rp4.500.000 | Rp4.500.000 | SUCCESS | SUCCESS | SUCCESS | `Audit_Clean` | Transaksi Cocok Sempurna (3-Way Matched) | **TEST PASSED** |
| 23 | `TRX-2026-0023` | 2303456789 | Rp80.000 | Rp80.000 | Rp80.000 | SUCCESS | SUCCESS | SUCCESS | `Audit_Clean` | Transaksi Cocok Sempurna (3-Way Matched) | **TEST PASSED** |
| 24 | `TRX-2026-0024` | 2404567890 | Rp1.250.000 | Rp1.250.000 | Rp1.250.000 | SUCCESS | SUCCESS | SUCCESS | `Audit_Clean` | Transaksi Cocok Sempurna (3-Way Matched) | **TEST PASSED** |
| 25 | `TRX-2026-0025` | 2505678901 | Rp670.000 | Rp670.000 | Rp670.000 | SUCCESS | SUCCESS | SUCCESS | `Audit_Clean` | Transaksi Cocok Sempurna (3-Way Matched) | **TEST PASSED** |

### Ringkasan Statistik Rekonsiliasi:
- **Total Transaksi Diproses**: 25 Transaksi (100%)
- **Total Transaksi Bersih (Clean / MATCHED)**: 17 Transaksi (68%)
- **Total Transaksi Exception (BRE)**: 8 Transaksi (32%)
- **Total Transaksi Fatal / Unhandled Error**: 0 Transaksi (0%)

---

## 3. Skenario Pengujian Tugas Khusus (Special Homework Tasks)

### TC-HW-01: Eksekusi Siklus Penuh 4-State REFramework (Tugas 1)
- **Tujuan**: Memastikan bot melewati 4 State State Machine standar UiPath tanpa henti mendadak.
- **Urutan Eksekusi**:
  1. `[STATE 1: INIT]`: Inisialisasi tabel audit, pembersihan proses browser lama via `KillProcess`, peluncuran sesi Chrome baru.
  2. `[STATE 2: GET DATA]`: Pengambilan item transaksi secara iteratif.
  3. `[STATE 3: PROCESS TRANSACTION]`: Pemrosesan 3-way matching dan validasi format.
  4. `[BRE HANDLED - NO RETRY]`: Transaksi dengan pelanggaran aturan bisnis (BRE) langsung dicatat ke `dtAuditExceptions` dan bot melanjutkan ke transaksi berikutnya tanpa auto-retry.
  5. `[STATE 4: END PROCESS]`: Setelah seluruh 25 data selesai, bot menutup Chrome, menulis laporan Excel, dan menampilkan ringkasan.
- **Status**: **TEST PASSED**

---

### TC-HW-02: Simulasi Browser Crash & Mekanisme Auto-Retry (Tugas 2)
- **Tujuan**: Memverifikasi ketahanan arsitektur terhadap kegagalan teknis/infrastruktur (*Self-Healing & Auto-Recovery*).
- **Kondisi Pengujian**:
  - Parameter `in_SimulateCrashAt = 7`.
  - Pada transaksi ke-7 (`TRX-2026-0007`), saat percobaan pertama (`retryCount == 0`), sub-proses memanggil terminasi proses Chrome (`chrome.exe`) dan melempar `System.Exception("SIMULATED CHROME CRASH ON TRANSACTION 7")`.
- **Hasil Verifikasi Alur**:
  1. `TryCatch` pada `State 3: Process Transaction` menangkap `System.Exception`.
  2. Bot mencatat log level Error: `[SE HANDLED - AUTO RETRY TRIGGERED]`.
  3. State Machine memeriksa kondisi transisi: `retryCount < maxRetry (1 < 3)`.
  4. Bot **bertransisi kembali ke State 1 (Initialization)** untuk memulihkan lingkungan kerja (*Re-Initialization*), membuka kembali browser Chrome, dan memuat ulang portal.
  5. State Machine mengambil kembali transaksi ke-7 pada State 2 dengan `retryCount = 1`.
  6. Pada percobaan kedua, kondisi simulasi crash dilewati, transaksi ke-7 sukses diproses (`MATCHED`).
  7. Bot mereset `retryCount = 0` dan melanjutkan transaksi ke-8 secara mulus.
- **Status**: **TEST PASSED**

---

### TC-HW-03: Penyempurnaan Laporan Excel 2 Sheet (Tugas 3)
- **Tujuan**: Memverifikasi pembagian sheet dan kelengkapan 10 kolom standar perbankan pada output Excel.
- **Lokasi File Output**: `Data/Output/Laporan_Rekonsiliasi_REFramework.xlsx`.
- **Kriteria Penerimaan**:
  1. Sheet `Audit_Clean` hanya memuat transaksi berstatus `MATCHED` (tepat 17 baris).
  2. Sheet `Audit_Exceptions` memuat seluruh transaksi berstatus `BRE` (tepat 8 baris).
  3. Kedua sheet memiliki struktur 10 kolom yang konsisten:
     1. `ID_Transaksi`
     2. `Waktu_Transaksi`
     3. `No_Rekening`
     4. `Channel`
     5. `Nominal_Core`
     6. `Nominal_Switch`
     7. `Nominal_Portal`
     8. `Status_Rekonsiliasi`
     9. `Keterangan_Audit`
     10. `Waktu_Audit`
- **Status**: **TEST PASSED**

---

### TC-DISPATCHER: Validasi Skema & Deteksi Duplikasi
- **Tujuan**: Memastikan integritas data input sebelum dikirim ke Queue.
- **Input File**:
  - `Core_Banking_Log.xlsx` (25 baris)
  - `Switching_Gateway_Log.xlsx` (24 baris, sengaja tidak ada `TRX-2026-0010`)
- **Validasi**:
  - Validasi kolom wajib lulus 100%.
  - Deteksi ID Transaksi ganda dengan `HashSet<string>` menghasilkan 0 duplicate.
  - Transaksi `TRX-2026-0010` tetap dipertahankan dengan `SwitchingStatus = "NOT_FOUND"` sehingga tidak terabaikan.
- **Status**: **TEST PASSED**

---

### TC-FALLBACK: Mekanisme Graceful Offline Fallback
- **Tujuan**: Menjamin otomasi tetap berjalan tanpa crash ketika UiPath Orchestrator belum terkoneksi atau offline.
- **Mekanisme**:
  - Aktivitas `ui:AddQueueItem` di Dispatcher dibungkus dalam blok `TryCatch`.
  - Jika terjadi koneksi gagal ke cloud tenant, transaksi tetap ditampung di `DataTable` memori lokal.
  - Pada `Performer_REFramework.xaml`, jika `ui:GetTransactionItem` mengembalikan null atau offline, bot secara otomatis beralih memproses data dari `in_dtTransactionData`.
- **Status**: **TEST PASSED**

