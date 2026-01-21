# Investigating suspicious login activity using SQL filters

## 📌 Table of contents

1. [Project overview](#overview)
2. [Investigation context](#scenario)
3. [Data sources](#data)
4. [Initial findings](#finding)
5. [After hours failed login attempts](#failed)
6. [Login attempts on specific dates](#specificdate)
7. [Login attempts outside authorized locations](#outside)
8. [Employee segmentation for security updates](#securityupdate)
9. [Final summary](#summary)

---

## 🧠 Project overview <a name="overview">

Studi ini menjadi bagian dari portofolio cybersecurity yang menunjukkan penggunaan SQL dalam proses investigasi keamanan. Skenario disusun dari sudut pandang seorang security analyst di organisasi berskala besar yang menelusuri aktivitas login mencurigakan dan mengidentifikasi karyawan yang berpotensi terdampak. Fokus utama diarahkan pada pengolahan data mentah di database supaya menghasilkan insight yang relevan untuk mendukung keputusan keamanan.

Analisis dilakukan langsung di tingkat database dengan memanfaatkan operator `AND`, `OR`, dan `NOT` untuk memfilter log login serta data karyawan. SQL digunakan untuk mengungkap login gagal di luar jam kerja, aktivitas pada tanggal tertentu, percobaan akses dari lokasi yang tidak diizinkan, serta pengelompokan karyawan berdasarkan departemen. Proyek ini mengacu pada materi Google Cybersecurity Professional Certificate, khususnya course Tools of the Trade: Linux and SQL, dengan penekanan pada SQL sebagai alat investigasi awal sebelum masuk ke tahap mitigasi.

---

## 🕵️ Investigation context <a name="context">

Studi ini menggambarkan peran saya sebagai bagian dari tim keamanan di organisasi besar dengan ribuan akun aktif dan perangkat yang saling terhubung. Sistem monitoring mulai memunculkan pola login yang tidak wajar, seperti akses di luar jam kerja dan percobaan masuk dari lokasi yang tidak seharusnya. Kondisi ini menuntut respons cepat supaya tidak berkembang menjadi insiden yang lebih besar.

Investigasi difokuskan pada dua sumber data utama, yaitu tabel `log_in_attempts` dan tabel `employees`. Data login digunakan untuk menelusuri pola akses mencurigakan, sementara data karyawan membantu mengidentifikasi perangkat dan departemen yang berpotensi terdampak. Seluruh analisis dilakukan langsung di level database supaya setiap temuan tetap akurat dan dapat dipertanggungjawabkan.

SQL menjadi alat utama untuk memfilter, mengelompokkan, dan mengecualikan data sesuai kebutuhan investigasi. Pendekatan ini membantu mengubah baris data mentah menjadi konteks yang jelas, sehingga tim security dapat memahami situasi dengan cepat dan menentukan langkah pengamanan berikutnya.

---

## 📊 Data sources <a name="data">

Investigasi menggunakan dua tabel utama yang tersedia di database internal dari materi kursus. Struktur ini membantu melihat konteks keamanan secara lebih utuh tanpa memisahkan data teknis dan identitas pengguna.

Tabel `log_in_attempts` menjadi titik awal analisis. Isi tabel mencatat seluruh aktivitas login, termasuk waktu, tanggal, status keberhasilan, serta informasi lokasi atau negara asal. Pola akses yang tidak normal dapat terlihat, seperti login gagal di luar jam kerja atau percobaan akses dari wilayah yang tidak termasuk area operasional.

<img width="645" height="324" alt="Screenshot 2026-01-21 at 19-23-00 Activity Filter with AND OR and NOT Google Skills" src="https://github.com/user-attachments/assets/f04d3e52-41f4-4048-8243-c32378c15480" />

Tabel `employees` berfungsi mengaitkan aktivitas login dengan identitas pengguna. Informasi karyawan, departemen, serta lokasi kantor atau gedung memberikan gambaran siapa yang terlibat dan perangkat mana yang berpotensi terdampak.

<img width="645" height="358" alt="Screenshot 2026-01-21 at 19-23-46 Activity Filter with AND OR and NOT Google Skills" src="https://github.com/user-attachments/assets/30f57fe3-d177-46c8-aba4-615de64deeb0" />

Pendekatan berbasis dua tabel ini membuat investigasi terasa lebih kontekstual. Aktivitas mencurigakan tidak berhenti sebagai baris data, tetapi dapat ditelusuri hingga ke pengguna dan unit kerja terkait. Alur ini memberi dasar yang jelas untuk menentukan langkah keamanan berikutnya.

---

## 🔍 Initial findings <a name="finding">

Investigasi diawali dengan meninjau log aktivitas login. Akses masuk sering menjadi titik awal penyalahgunaan sistem dari sudut pandang keamanan. Setiap percobaan login menyimpan informasi waktu, lokasi, dan status keberhasilan, sehingga data ini memberi gambaran awal tentang kondisi sistem yang sebenarnya.

Peninjauan awal menunjukkan pola yang tidak sesuai dengan kebiasaan kerja normal. Percobaan login muncul di luar jam operasional dan beberapa akses tercatat dari lokasi yang jarang digunakan. Situasi ini belum bisa langsung dianggap sebagai insiden, namun cukup kuat untuk memicu analisis lanjutan.

Analisis kemudian difokuskan menggunakan filter SQL untuk mempersempit data. Perhatian diarahkan pada informasi yang paling relevan sebelum masuk ke tahap query secara detail. Alur ini menjaga proses investigasi tetap rapi dan berbasis prioritas, dengan fokus pada indikasi risiko yang paling jelas terlebih dahulu.

---
