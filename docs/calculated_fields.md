# Looker Studio Calculated Fields (Formula Log)

Dokumen ini mencatat rumus asli (*calculated fields*) yang digunakan dalam dashboard Looker Studio beserta penjelasan logika di balik setiap formula tersebut.

---

## 1. `is_mayor`
Digunakan untuk menentukan apakah mata kuliah yang diambil tergolong sebagai mata kuliah wajib/utama Departemen Ilmu Komputer (Mayor) atau mata kuliah pilihan/penunjang (EC/SC - *Elective Courses / Supporting Courses*).

* **Nama Kolom**: `is_mayor`
* **Tipe Data**: Text (String)
* **Formula**:
```sql
CASE
  WHEN STARTS_WITH(UPPER(TRIM(Kode)), "KOM") THEN "Mayor"
  ELSE "EC/SC"
END
```
* **Penjelasan Logika**:
  1. `TRIM(Kode)` menghapus spasi kosong di awal maupun di akhir kode mata kuliah untuk menghindari kesalahan deteksi.
  2. `UPPER(...)` mengubah semua karakter pada kode mata kuliah menjadi huruf kapital (case-insensitive check).
  3. `STARTS_WITH(..., "KOM")` memeriksa apakah kode mata kuliah tersebut diawali dengan teks `"KOM"` (kode departemen Ilmu Komputer).
  4. Jika kondisi terpenuhi, baris data diklasifikasikan sebagai `"Mayor"`. Jika tidak, diklasifikasikan sebagai `"EC/SC"`.

---

## 2. `nilaimutu`
Digunakan untuk mengonversi nilai huruf (`HurufMutu`) menjadi nilai angka numerik indeks prestasi (skala 4.0) untuk kalkulasi matematis seperti rata-rata IPS mahasiswa.

* **Nama Kolom**: `nilaimutu`
* **Tipe Data**: Number (Decimal)
* **Formula**:
```sql
CASE HurufMutu
  WHEN "A" THEN 4.0
  WHEN "AB" THEN 3.5
  WHEN "B" THEN 3.0
  WHEN "BC" THEN 2.5
  WHEN "C" THEN 2.0
  WHEN "D" THEN 1.0
  WHEN "E" THEN 0.0
  ELSE NULL
END
```
* **Penjelasan Logika**:
  Melakukan pencocokan langsung (*simple CASE*) pada kolom `HurufMutu`. Setiap huruf mutu yang valid dipetakan ke angka IP standar IPB (misal: `"A"` menjadi `4.0`, `"AB"` menjadi `3.5`, dst.). Nilai di luar itu (seperti `"BL"` atau kosong) akan menghasilkan `NULL` sehingga tidak merusak perhitungan rata-rata.

---

## 3. `is_lulus`
Digunakan untuk membagi performa mahasiswa ke dalam status kelulusan di suatu mata kuliah.

* **Nama Kolom**: `is_lulus`
* **Tipe Data**: Text (String)
* **Formula**:
```sql
CASE
  WHEN HurufMutu IN ("D","E") THEN "Tidak Lulus"
  WHEN HurufMutu = "BL" THEN "Belum Dinilai"
  ELSE "Lulus"
END
```
* **Penjelasan Logika**:
  1. `WHEN HurufMutu IN ("D","E")` mengecek jika nilai mahasiswa adalah `"D"` atau `"E"`. Jika benar, statusnya dikelompokkan sebagai `"Tidak Lulus"`.
  2. `WHEN HurufMutu = "BL"` mengecek jika nilai mahasiswa bertuliskan `"BL"` (Belum Lengkap). Jika benar, statusnya dikelompokkan sebagai `"Belum Dinilai"`.
  3. `ELSE` mencakup semua nilai huruf mutu lainnya (seperti `"A"`, `"AB"`, `"B"`, `"BC"`, `"C"`) dan mengelompokkannya sebagai `"Lulus"`.

---

## 4. `TipeMK`
Digunakan untuk mengategorikan asal kelompok mata kuliah berdasarkan awalan kodenya untuk menganalisis komposisi pengambilan mata kuliah mahasiswa.

* **Nama Kolom**: `TipeMK`
* **Tipe Data**: Text (String)
* **Formula**:
```sql
CASE
  WHEN REGEXP_MATCH(Kode, "^KOM") THEN "Program Studi"
  WHEN REGEXP_MATCH(Kode, "^IPB") THEN "IPB (Umum)"
  WHEN REGEXP_MATCH(Kode, "^MOOC") THEN "MOOC"
  WHEN REGEXP_MATCH(Kode, "^EXC") THEN "Exchange"
  ELSE "Lintas Prodi"
END
```
* **Penjelasan Logika**:
  Menggunakan pencocokan pola ekspresi reguler (`REGEXP_MATCH`) untuk mendeteksi prefix/awalan kode mata kuliah:
  * Awalan `^KOM` (misal KOM120B) dikategorikan sebagai mata kuliah internal **"Program Studi"** Ilmu Komputer.
  * Awalan `^IPB` (misal IPB210A) dikategorikan sebagai mata kuliah wajib universitas **"IPB (Umum)"**.
  * Awalan `^MOOC` dikategorikan sebagai pembelajaran digital **"MOOC"**.
  * Awalan `^EXC` dikategorikan sebagai program pertukaran mahasiswa **"Exchange"**.
  * Kode lain di luar empat pola tersebut (misal departemen lain seperti `MAT`, `STK`, `BING`) dikelompokkan sebagai **"Lintas Prodi"**.
