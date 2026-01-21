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

## 🕒 After hours failed login attempts <a name="failed">

Tahap awal analisis teknis menyoroti percobaan login gagal yang terjadi di luar jam kerja. Pola seperti ini sering muncul saat ada upaya akses tidak sah, terutama ketika sistem seharusnya jarang digunakan. Waktu akses menjadi indikator penting karena aktivitas setelah jam operasional biasanya membawa risiko lebih tinggi.

Identifikasi pola dilakukan dengan memfilter tabel  `log_in_attempts` menggunakan dua kriteria utama. Batas waktu login ditetapkan setelah pukul 18.00. Status login dibatasi hanya pada percobaan yang gagal. Operator `AND` memastikan kedua kondisi tersebut terpenuhi dalam satu query.

<img width="645" height="426" alt="Screenshot 2026-01-21 at 23-23-27 Activity Filter with AND OR and NOT Google Skills" src="https://github.com/user-attachments/assets/c6df27e9-c0ea-4cc1-b21e-448fbc657e11" />

Hasil query menampilkan 19 entri yang seluruhnya merupakan login gagal setelah jam kerja. Aktivitas tercatat pada malam hari hingga mendekati tengah malam. Lokasi akses tersebar di beberapa negara seperti `US`, `CAN`, `MEXICO`, `USA`, dan `CANADA`, yang menunjukkan sumber percobaan tidak berasal dari satu wilayah saja.

Kemunculan beberapa username secara berulang dalam rentang tanggal yang berdekatan mengindikasikan percobaan akses berulang. Pola waktu yang tidak wajar, status login gagal, dan variasi lokasi menjadikan data ini relevan sebagai prioritas investigasi. Temuan ini memberi dasar yang jelas untuk melanjutkan analisis ke detail tanggal dan lokasi pada tahap berikutnya.

---

## 📅 Login attempts on specific dates <a name="specificdate">

Setelah pola login di luar jam kerja terlihat jelas, arah investigasi difokuskan pada tanggal yang memicu peringatan sistem. Tanggal 9 Mei 2022 menjadi titik perhatian karena bertepatan dengan peningkatan aktivitas login. Data dari satu hari sebelumnya ikut dianalisis agar konteks kejadian bisa dipahami dengan lebih utuh.

Pemfilteran data dilakukan menggunakan SQL dengan logika `OR`. Pendekatan ini memungkinkan dua tanggal dianalisis dalam satu query tanpa memisahkan alur data. Tujuan utama tetap pada membaca pola aktivitas secara menyeluruh, bukan sekadar menyoroti satu kejadian tunggal.

<img width="645" height="470" alt="Screenshot 2026-01-21 at 23-36-40 Activity Filter with AND OR and NOT Google Skills" src="https://github.com/user-attachments/assets/5f71a263-4dda-4595-9496-f494d8d4e648" />

Hasil query menghasilkan 75 baris data yang merekam seluruh percobaan login pada kedua tanggal tersebut. Aktivitas tersebar dari dini hari hingga malam. Sebagian login berhasil, sementara cukup banyak yang gagal, menunjukkan intensitas percobaan akses yang tinggi dalam waktu singkat.

Beberapa username muncul berulang pada dua tanggal berbeda dengan status login yang bervariasi. Pola ini memberi indikasi adanya percobaan akses berulang yang perlu diperhatikan. Data lokasi menunjukkan asal login dari berbagai negara dengan format yang tidak selalu konsisten, menandakan sumber akses yang beragam.

Temuan ini menjadi jembatan antara analisis waktu dan lokasi. Pemahaman terhadap aktivitas pada tanggal tertentu membantu mempersempit fokus investigasi ke tahap berikutnya dengan dasar data yang lebih terarah dan relevan.

---

## 🌐 Login attempts outside authorized locations <a name="outside">

Setelah analisis waktu dan tanggal selesai, perhatian diarahkan ke faktor lokasi. Tim keamanan telah memastikan bahwa aktivitas mencurigakan tidak berasal dari Meksiko, sehingga fokus difokuskan pada seluruh login dari luar wilayah tersebut. Lokasi digunakan sebagai petunjuk awal untuk melihat kemungkinan akses lintas negara yang tidak sesuai dengan pola kerja normal.

Pemfilteran dilakukan menggunakan SQL dengan kombinasi `NOT` dan `LIKE` pada kolom country. Pendekatan ini dipilih karena penulisan nama negara tidak selalu konsisten. Entri seperti `MEX` dan `MEXICO` tetap dapat dikelompokkan dengan pola yang sama, lalu dikecualikan dari hasil menggunakan `NOT`.

<img width="645" height="466" alt="Screenshot 2026-01-21 at 23-48-09 Activity Filter with AND OR and NOT Google Skills" src="https://github.com/user-attachments/assets/8318262b-2d0f-41fa-8d22-002a85e1e759" />

Query tersebut menghasilkan 144 baris data yang seluruhnya berasal dari negara selain Meksiko. Aktivitas login datang dari beberapa wilayah seperti `US`, `USA`, `CAN`, dan `CANADA`. Pola ini menunjukkan bahwa sebagian besar akses sistem memang terjadi dari luar lokasi yang sebelumnya dianggap aman.

Hasil data memperlihatkan kombinasi login berhasil dan gagal. Beberapa akun mencoba masuk berulang kali dari negara yang sama dalam waktu berdekatan. Pola ini mengarah pada kemungkinan percobaan brute force ringan atau penggunaan kredensial yang tidak valid dari luar wilayah operasional utama.

Temuan ini memperjelas konteks risiko yang muncul sebelumnya. Pengecualian Meksiko membantu mempersempit fokus analisis pada sumber akses eksternal yang perlu dipantau lebih ketat di tahap investigasi selanjutnya.

## 👥 Employee segmentation for security updates <a name="securityupdate">

### a. Marketing department

Analisis kemudian diarahkan ke segmentasi karyawan. Fokusnya mengidentifikasi kelompok pengguna yang perlu diprioritaskan dalam pembaruan keamanan, terutama terkait perangkat dan akses ke sistem internal. Data diambil dari tabel `employees` dengan dua filter utama, yaitu departemen dan lokasi kantor. Departemen Marketing dipilih karena sering berinteraksi dengan pihak eksternal. Kantor dengan awalan East digunakan sebagai penanda satu klaster lokasi fisik yang perlu ditinjau lebih dalam.

Query berikut menggabungkan kedua kriteria tersebut menggunakan operator `AND` dan `LIKE` untuk mencocokkan pola lokasi kantor:

<img width="645" height="234" alt="Screenshot 2026-01-22 at 00-01-20 Activity Filter with AND OR and NOT Google Skills" src="https://github.com/user-attachments/assets/5b5889a9-76c7-470f-be01-0b4737e1add4" />

Hasil query menunjukkan tujuh karyawan dari departemen Marketing yang bekerja di kantor wilayah East. Data ini menampilkan informasi `username` dan `device_id` yang terhubung langsung dengan sistem organisasi. Output memperlihatkan sebagian besar karyawan memiliki `device_id` yang tercatat. Satu entri muncul dengan nilai `NULL`, menandakan perangkat yang belum terdaftar atau belum terhubung ke sistem manajemen aset. Kondisi ini perlu diperhatikan karena perangkat tanpa identifikasi berisiko tidak menerima pembaruan keamanan atau kebijakan kontrol akses.

Segmentasi ini membantu tim keamanan menentukan sasaran distribusi security update secara lebih tepat. Identifikasi pengguna, departemen, dan lokasi memungkinkan tindakan lanjutan dilakukan secara fokus tanpa mengganggu operasional organisasi secara keseluruhan.

### b. Finance or sales department

Tahap segmentasi selanjutnya diarahkan pada karyawan dari departemen Finance dan Sales. Kedua tim ini dipilih karena menangani data sensitif dan memiliki frekuensi akses sistem yang tinggi, sehingga sering menjadi fokus utama dalam pembaruan keamanan dan pengaturan kontrol akses. Pengelompokan dua departemen dilakukan dalam satu proses menggunakan operator `OR`. Pendekatan ini memudahkan penarikan data tanpa perlu menjalankan query terpisah untuk masing-masing departemen.

Query SQL yang digunakan sebagai berikut:

<img width="645" height="406" alt="Screenshot 2026-01-22 at 00-15-10 Activity Filter with AND OR and NOT Google Skills" src="https://github.com/user-attachments/assets/8f594dcf-9cc9-41f6-9097-ac12e1e98433" />

Hasil query menampilkan 71 karyawan dari kedua departemen tersebut. Data yang muncul mencakup `employee_id`, `username`, `department`, `office`, dan `device_id` yang penting untuk pengelolaan akun serta keamanan perangkat. Sebaran lokasi karyawan terlihat cukup luas, mencakup kantor North, South, East, West, hingga Central. Kondisi ini menunjukkan bahwa pembaruan keamanan perlu direncanakan lintas wilayah, tidak hanya terfokus pada satu lokasi.

Beberapa baris data menunjukkan `device_id` bernilai `NULL`. Situasi ini menandakan adanya perangkat yang belum tercatat di sistem manajemen aset. Dari sudut pandang keamanan, perangkat tanpa identitas berisiko tertinggal dari pembaruan atau kebijakan proteksi yang berlaku.Segmentasi menggunakan logika `OR` membantu tim keamanan menyusun prioritas secara lebih rapi. Pemetaan berdasarkan departemen kritis memungkinkan langkah mitigasi dilakukan secara selektif dan terkontrol tanpa berdampak langsung ke seluruh organisasi.

### c. Employees outside IT

Tahap ini menekankan segmentasi dengan exclusion filtering untuk memisahkan karyawan non IT dari proses tertentu dalam pembaruan keamanan. Tim IT biasanya memiliki kebijakan, jadwal, dan mekanisme pembaruan berbeda dibanding departemen lain. Pengecualian dilakukan menggunakan operator NOT di SQL, sehingga seluruh karyawan selain dari departemen IT dapat ditarik dalam satu query.

Berikut adalah query SQL yang digunakan:

<img width="645" height="416" alt="Screenshot 2026-01-22 at 01-24-33 Activity Filter with AND OR and NOT Google Skills" src="https://github.com/user-attachments/assets/e40f9fab-221a-4608-90ff-c0677bedb34b" />

Query menghasilkan 161 karyawan dari berbagai departemen selain IT, termasuk Marketing, Finance, Sales, dan Human Resources. Data mencakup `employee_id`, `username`, `department`, `office`, dan `device_id` yang penting untuk memetakan cakupan pembaruan keamanan. Sebagian besar karyawan berada di luar IT dan tersebar di berbagai lokasi kantor. Kondisi ini menunjukkan kebutuhan untuk membedakan kebijakan security update antara tim teknis dan non teknis, terutama terkait waktu penerapan dan intervensi sistem.

Beberapa entri `device_id` masih bernilai `NULL`, menandakan perangkat yang belum tercatat di inventaris dan berpotensi tidak menerima pembaruan otomatis. Exclusion filtering dengan `NOT` logic memungkinkan segmentasi yang lebih strategis. Tim keamanan dapat fokus mendistribusikan pembaruan ke kelompok relevan tanpa melibatkan IT di proses yang tidak diperlukan, menjaga efisiensi operasional sekaligus keamanan.

---

## 👥 Final summary <a name="summary">

Investigasi ini menutup rangkaian analisis dengan satu pemahaman utama bahwa data menjadi bermakna ketika difilter dengan konteks yang tepat. Melalui serangkaian query SQL, setiap potongan informasi yang awalnya tersebar dapat disusun menjadi pola yang relevan untuk kebutuhan keamanan.

Dari proses ini, terlihat jelas bahwa penggunaan operator `AND`, `OR`, dan `NOT` bukan sekadar teknik query, melainkan alat investigasi. Filtering berdasarkan waktu login, status keberhasilan, tanggal tertentu, hingga segmentasi karyawan memungkinkan identifikasi risiko dilakukan secara terarah. Tanpa filtering, anomali seperti login gagal di luar jam kerja atau perangkat tanpa identitas akan mudah tersembunyi di balik volume data yang besar.

Investigasi ini juga memperkuat pemahaman bahwa keamanan siber tidak selalu dimulai dari serangan yang kompleks. Banyak indikasi awal justru muncul dari aktivitas sederhana seperti upaya login dan distribusi akses pengguna. SQL berperan sebagai jembatan antara data mentah dan keputusan keamanan, membantu analis menarik kesimpulan berbasis bukti, bukan asumsi.

Sebagai penutup, studi ini menunjukkan keterkaitan yang kuat antara SQL, proses investigasi, dan praktik cybersecurity. SQL menyediakan cara sistematis untuk menggali data, investigasi memberikan kerangka berpikir analitis, dan keamanan siber memastikan setiap temuan diterjemahkan menjadi tindakan yang relevan. Kombinasi ketiganya membentuk fondasi penting bagi profesional keamanan dalam menjaga sistem tetap aman, terkontrol, dan siap menghadapi risiko yang terus berkembang.
