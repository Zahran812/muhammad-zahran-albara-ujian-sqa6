# Panduan Pengaturan & Eksekusi Proyek (Setup Guide)
## Day 30 Banking Reconciliation Enterprise REFramework

Dokumen ini menyediakan panduan komprehensif langkah demi langkah untuk mengonfigurasi, membuka, dan menjalankan proyek otomasi **Day30_BankingReconciliation** pada UiPath Studio (Windows C#).

---

## 1. Prasyarat Sistem & Lingkungan (Prerequisites)

Sebelum membuka proyek, pastikan perangkat kerja memenuhi persyaratan berikut:

| Komponen | Spesifikasi / Rekomendasi | Keterangan |
| :--- | :--- | :--- |
| **Sistem Operasi** | Windows 10 / 11 64-bit | Didukung penuh oleh Windows Target Framework |
| **UiPath Studio** | Studio Community / Enterprise 2024.10+ / 2026.10+ | Target Framework: `Windows`, Bahasa: `CSharp` |
| **Web Browser** | Google Chrome (Versi Terbaru) | Digunakan untuk portal `WebPortal/banking-monitoring-portal.html` |
| **Ekstensi UiPath** | UiPath Web Automation Extension for Chrome | Diaktifkan via `UiPath Studio > Home > Tools > UiPath Extensions` |
| **Microsoft Excel** | Terpasang (Excel 2016/2019/365) | Diperlukan untuk aktivitas `ExcelProcessScopeX` / Modern Excel |
| **UiPath CLI** | `uip` CLI (opsional / CI-CD) | Terpasang di path instalasi UiPath Studio |

---

## 2. Struktur Direktori Proyek

Proyek ini menggunakan struktur standar enterprise modular:

```text
Day30_BankingReconciliation/
│
├── .objects/                                  # Object Repository UI element definitions
├── .settings/                                 # Konfigurasi workspace Studio
├── Data/
│   ├── Input/
│   │   ├── Core_Banking_Log.xlsx              # 25 log transaksi dari sistem Core Banking
│   │   └── Switching_Gateway_Log.xlsx         # 24 log transaksi dari Switching Gateway
│   ├── Output/
│   │   └── Laporan_Rekonsiliasi_REFramework.xlsx # Laporan akhir rekonsiliasi (Audit_Clean & Audit_Exceptions)
│   └── Temp/                                  # Direktori penyimpanan sementara
│
├── docs/
│   ├── IMPLEMENTATION_REPORT.md               # Laporan teknis arsitektur & implementasi
│   ├── SETUP_GUIDE.md                         # Panduan setup & eksekusi (dokumen ini)
│   ├── TEST_CASES.md                          # Matriks pengujian 25 transaksi & skenario crash
│   └── ORCHESTRATOR_SETUP.md                  # Panduan konfigurasi Orchestrator Cloud Queue
│
├── WebPortal/
│   └── banking-monitoring-portal.html         # Portal web simulasi monitoring transaksi perbankan
│
├── Workflows/
│   ├── Dispatcher.xaml                        # Multi-source ingestion & Queue feeder
│   ├── ProcessTransaction.xaml                # Logika bisnis 3-way matching & crash simulator
│   └── Performer_REFramework.xaml             # State Machine REFramework 4 fase
│
├── Main.xaml                                  # Master Launcher pipeline
├── project.json                               # Manifest dependensi & konfigurasi C#
└── AGENTS.md                                  # Aturan tata kelola proyek
```

---

## 3. Dependensi Paket NuGet (project.json)

Proyek ini telah dikonfigurasi dengan dependensi modern yang teruji:
- **`UiPath.Excel.Activities`**: Versi `[2.25.1-preview]` — modern Excel automation (`ExcelProcessScopeX`).
- **`UiPath.System.Activities`**: Versi `[25.4.1-preview]` — core system activities, collections, and queue management.
- **`UiPath.UIAutomation.Activities`**: Versi `[25.10.1-preview]` — modern UI Automation (`NApplicationCard`, `NTypeInto`, `NClick`, `NGetText`).

> [!NOTE]
> Saat proyek pertama kali dibuka pada UiPath Studio, Studio akan secara otomatis melakukan pemulihan (*restore dependencies*) seluruh paket NuGet di atas. Pastikan komputer terhubung ke internet saat proses pemulihan pertama kali.

---

## 4. Konfigurasi Path & Parameter

Seluruh alur kerja menggunakan resolusi path dinamis yang independen terhadap lokasi folder pengguna (`System.IO.Path.Combine(Environment.CurrentDirectory, ...)`). Anda tidak perlu mengubah path secara manual ketika memindahkan folder proyek ke mesin lain.

### Parameter Penting pada `Main.xaml` & `Performer_REFramework.xaml`:
1. **`in_QueueName`** (Default: `"BankingReconciliationQueue"`):
   - Nama antrean Orchestrator Cloud yang ditargetkan oleh Dispatcher dan Performer.
2. **`in_SimulateCrashAt`** (Default: `7`):
   - Nomor transaksi yang akan memicu simulasi browser crash (Tugas 2 Homework).
   - **Tips**: Jika Anda ingin menjalankan otomasi secara penuh tanpa crash, ubah nilai parameter ini menjadi `0`.
3. **`in_MaxRetry`** (Default: `3`):
   - Jumlah batas maksimum percobaan ulang (*auto-retry*) jika terjadi `SystemException`.

---

## 5. Langkah-Langkah Menjalankan Otomasi

### Opsi A: Menjalankan Seluruh Pipeline via `Main.xaml` (Direkomendasikan)
1. Buka **UiPath Studio**.
2. Pilih menu **Open a Local Project** dan arahkan ke direktori `Day30_BankingReconciliation`.
3. Tunggu hingga status dependensi di pojok kanan bawah menunjukkan status **Ready** (hijau).
4. Buka file [Main.xaml](file:///c:/Users/M%20Zahran%20A/Documents/SQA%20Bootcamp/day30/Day30_BankingReconciliation/Main.xaml) pada panel *Project*.
5. Klik tombol **Run File** (atau tekan `F5` / `Ctrl+F5`).
6. Bot akan:
   - Menjalankan `Dispatcher.xaml` untuk membaca data Excel, memvalidasi skema, dan mengisi antrean.
   - Menjalankan `Performer_REFramework.xaml` yang menginisialisasi browser Chrome membuka `WebPortal/banking-monitoring-portal.html`.
   - Melakukan simulasi crash pada transaksi ke-7, me-restart Chrome, dan melakukan auto-retry hingga sukses.
   - Memproses 25 transaksi hingga selesai.
   - Menghasilkan laporan Excel final di `Data/Output/Laporan_Rekonsiliasi_REFramework.xlsx`.

---

### Opsi B: Menjalankan Dispatcher Secara Terpisah
Jika Anda hanya ingin mengisi antrean Orchestrator atau menguji validasi file input:
1. Buka [Workflows/Dispatcher.xaml](file:///c:/Users/M%20Zahran%20A/Documents/SQA%20Bootcamp/day30/Day30_BankingReconciliation/Workflows/Dispatcher.xaml).
2. Klik **Run File** (`Ctrl+F5`).
3. Output panel akan menampilkan log jumlah data yang berhasil dibaca dari Core Banking (25) dan Switching Gateway (24), status validasi skema kolom, serta hasil pemeriksaan duplikasi (`0 duplicate`).

---

### Opsi C: Menjalankan Performer Secara Terpisah
Jika antrean Orchestrator sudah terisi data transaksi:
1. Buka [Workflows/Performer_REFramework.xaml](file:///c:/Users/M%20Zahran%20A/Documents/SQA%20Bootcamp/day30/Day30_BankingReconciliation/Workflows/Performer_REFramework.xaml).
2. Pastikan parameter `in_QueueName` sesuai dengan nama antrean di Orchestrator Anda.
3. Klik **Run File** (`Ctrl+F5`).
4. Bot akan langsung masuk ke State 1 (Initialization), mengambil item dari Orchestrator Queue pada State 2, dan memproses transaksi pada State 3.

---

## 6. Verifikasi Hasil Output

Setelah eksekusi selesai:
1. Buka folder [Data/Output](file:///c:/Users/M%20Zahran%20A/Documents/SQA%20Bootcamp/day30/Day30_BankingReconciliation/Data/Output).
2. Buka file [Laporan_Rekonsiliasi_REFramework.xlsx](file:///c:/Users/M%20Zahran%20A/Documents/SQA%20Bootcamp/day30/Day30_BankingReconciliation/Data/Output/Laporan_Rekonsiliasi_REFramework.xlsx).
3. Verifikasi ketersediaan 2 sheet:
   - **`Audit_Clean`**: Berisi **17 baris transaksi** berstatus `MATCHED`.
   - **`Audit_Exceptions`**: Berisi **8 baris transaksi** berstatus `BRE` dengan rincian `Variance Amount`, `Variance Status`, `Invalid Account`, `Zero/Negative Amount`, atau `NOT FOUND IN SWITCH`.
4. Periksa bahwa seluruh 10 kolom standar perbankan terisi lengkap:
   `ID_Transaksi`, `Waktu_Transaksi`, `No_Rekening`, `Channel`, `Nominal_Core`, `Nominal_Switch`, `Nominal_Portal`, `Status_Rekonsiliasi`, `Keterangan_Audit`, `Waktu_Audit`.

---

## 7. Troubleshooting & Solusi Permasalahan

| Gejala Masalah | Penyebab Umum | Langkah Solusi |
| :--- | :--- | :--- |
| **Error: "The Use Application/Browser activity is not yet configured"** | Studio mendeteksi selector Chrome tidak sinkron | Buka `ProcessTransaction.xaml`, pastikan aktivitas `NApplicationCard` mengarah ke file `banking-monitoring-portal.html`. |
| **Browser Chrome tidak membuka portal web** | Jalur relatif portal tidak ditemukan | Pastikan folder `WebPortal` berada satu tingkat di bawah root project dan file `banking-monitoring-portal.html` ada di dalamnya. |
| **Warning: Cannot connect to Orchestrator** | Robot belum login ke Automation Cloud | Tidak masalah: Bot memiliki mekanisme *graceful offline fallback* yang akan beralih menggunakan DataTable lokal secara otomatis tanpa crash. Untuk mengaktifkan integrasi Cloud penuh, lihat [ORCHESTRATOR_SETUP.md](file:///c:/Users/M%20Zahran%20A/Documents/SQA%20Bootcamp/day30/Day30_BankingReconciliation/docs/ORCHESTRATOR_SETUP.md). |
| **Excel file locked / Sharing violation** | File Excel input atau output sedang dibuka oleh pengguna | Tutup aplikasi Microsoft Excel sebelum menjalankan bot agar file dapat dibaca dan ditimpa secara eksklusif. |
| **UiPath Extension Error pada Chrome** | Ekstensi UiPath pada browser belum diaktifkan | Buka Chrome, navigasikan ke `chrome://extensions`, pastikan **UiPath Web Automation** aktif dan opsi *Allow access to file URLs* dicentang. |

