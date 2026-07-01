# Data Dictionary

Dokumen ini mendefinisikan skema, tipe data, dan deskripsi kolom dari dataset utama (contoh format pada berkas **`data/sample_krs_merged.csv`**) yang diimpor ke Looker Studio.

---

## Tabel Utama (`sample_krs_merged.csv`)

Tabel gabungan ini berisi histori lengkap akademik mahasiswa, detail mata kuliah, dan nilai yang diperoleh.

| Nama Kolom | Tipe Data | Deskripsi | Contoh Nilai |
| :--- | :--- | :--- | :--- |
| **NIM** | Text | Nomor Induk Mahasiswa IPB | `G6401200001` |
| **Nama** | Text | Nama lengkap mahasiswa | `Mahasiswa Dummy 1` |
| **KodeProgramStudi** | Text | Kode internal program studi | `M03` |
| **ProgramStudi** | Text | Nama Program Studi asal mahasiswa | `Ilmu Komputer` |
| **TahunMasuk** | Integer | Tahun angkatan masuk kuliah | `2020` |
| **TahunAkademik** | Text | Periode tahun ajaran kuliah | `2025/2026` |
| **SemesterAkademik**| Integer | Semester akademik (1 = Ganjil, 2 = Genap) | `1` |
| **SemesterMahasiswa**| Integer | Semester aktif mahasiswa saat mengambil mata kuliah | `11` |
| **Kode** | Text | Kode unik mata kuliah | `KOM1499` |
| **Matakuliah** | Text | Nama resmi mata kuliah | `Tugas Akhir` |
| **SKS** | Integer | Total beban kredit mata kuliah (SKS) | `4` |
| **SKSKuliah** | Integer | SKS untuk perkuliahan tatap muka/teori | `0` |
| **SKSPraktikum** | Integer | SKS untuk praktikum/laboratorium | `4` |
| **HurufMutu** | Text | Nilai huruf kelulusan kelas | `BL` |

---

## Kolom Kalkulasi Tambahan (*Calculated Fields*)

Kolom berikut dibentuk secara dinamis di Looker Studio berdasarkan data mentah di atas.

| Nama Kolom | Tipe Data | Deskripsi | Kolom Acuan | Logika Formula |
| :--- | :--- | :--- | :--- | :--- |
| **is_mayor** | Text | Mengelompokkan mata kuliah ke dalam kategori wajib utama departemen (`Mayor`) atau mata kuliah penunjang/pilihan (`EC/SC`). | `Kode` | Mengecek apakah awalan kode mata kuliah adalah `KOM` (Ilmu Komputer). |
| **nilaimutu** | Number | Angka indeks prestasi (skala 0.00 - 4.00) | `HurufMutu` | Mengonversi huruf mutu (A s.d E) menjadi angka indeks prestasi. |
| **is_lulus** | Text | Status kelulusan mahasiswa (`Lulus`, `Tidak Lulus`, `Belum Dinilai`) | `HurufMutu` | Menandai nilai D/E sebagai tidak lulus, BL sebagai belum dinilai, sisanya lulus. |
| **TipeMK** | Text | Jenis kategori mata kuliah (`Program Studi`, `IPB (Umum)`, `MOOC`, `Exchange`, `Lintas Prodi`) | `Kode` | Memetakan awalan kode mata kuliah ke kategori struktur kurikulum. |

Untuk melihat rumus lengkap kolom kalkulasi ini, silakan merujuk pada dokumen **[Calculated Fields](calculated_fields.md)**.
