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

Studi kasus ini dibuat sebagai bagian dari portofolio cybersecurity untuk menunjukkan penggunaan SQL dalam investigasi keamanan. Skenario dibangun seolah saya sebagai security analyst di organisasi besar, menelusuri login mencurigakan dan mengidentifikasi karyawan yang mungkin terdampak. Fokus utama adalah mengolah data mentah di database supaya bisa mendapatkan insight yang berguna untuk keputusan keamanan.

Analisis dilakukan langsung di database dengan memakai operator `AND`, `OR`, dan `NOT` untuk memfilter log login dan data karyawan. SQL dipakai untuk menemukan login gagal di luar jam kerja, aktivitas pada tanggal tertentu, percobaan akses dari lokasi tidak sah, serta mengelompokkan karyawan berdasarkan departemen. Proyek ini mengacu pada materi Google Cybersecurity Professional Certificate, khususnya course Tools of the Trade: Linux and SQL, dengan SQL sebagai alat investigasi awal sebelum tahap mitigasi dimulai.

---

## 🕵️ Investigation context <a name="context">

Studi kasus ini menggambarkan peran saya sebagai anggota tim keamanan di organisasi besar dengan ribuan akun dan perangkat yang terhubung. Sistem monitoring mulai menunjukkan pola login aneh, seperti akses di luar jam kerja dan percobaan masuk dari lokasi yang tidak seharusnya. Kondisi ini menuntut respons cepat supaya insiden tidak melebar.

Investigasi fokus pada dua tabel utama, `log_in_attempts` dan `employees`. Data login dipakai untuk menelusuri pola akses mencurigakan, sementara data karyawan membantu mengetahui perangkat dan departemen yang mungkin terdampak. Analisis dilakukan langsung di database supaya hasilnya akurat dan bisa dipertanggungjawabkan.

SQL digunakan untuk memfilter, mengelompokkan, dan mengecualikan data sesuai kebutuhan investigasi. Pendekatan ini mengubah baris data mentah menjadi konteks yang jelas, sehingga tim security cepat memahami situasi dan bisa menentukan langkah pengamanan berikutnya.

---

## 📊 Data sources <a name="data">

Investigasi menggunakan dua tabel utama yang tersedia di database internal dari materi kursus. Struktur ini membantu melihat konteks keamanan lebih lengkap tanpa memisahkan data teknis dan identitas pengguna.

Tabel `log_in_attempts` menjadi titik awal analisis. Isinya mencatat semua aktivitas login, termasuk tanggal, waktu, status keberhasilan, dan lokasi atau negara asal. Pola akses yang tidak normal terlihat jelas, seperti login gagal di luar jam kerja atau percobaan masuk dari wilayah yang bukan area operasional.

<img width="645" height="324" alt="Screenshot 2026-01-21 at 19-23-00 Activity Filter with AND OR and NOT Google Skills" src="https://github.com/user-attachments/assets/f04d3e52-41f4-4048-8243-c32378c15480" />

Tabel `employees` mengaitkan aktivitas login dengan identitas pengguna. Informasi karyawan, departemen, dan lokasi kantor menunjukkan siapa yang terlibat dan perangkat mana yang mungkin terdampak.

<img width="645" height="358" alt="Screenshot 2026-01-21 at 19-23-46 Activity Filter with AND OR and NOT Google Skills" src="https://github.com/user-attachments/assets/30f57fe3-d177-46c8-aba4-615de64deeb0" />

Pendekatan pakai dua tabel ini membuat investigasi lebih kontekstual. Aktivitas mencurigakan tidak hanya berupa baris data, tetapi bisa ditelusuri sampai ke pengguna dan unit kerjanya. Alur ini memberi dasar jelas untuk menentukan langkah keamanan berikutnya.

---

## 🔍 Initial findings <a name="finding">

Investigasi dimulai dengan meninjau log aktivitas login. Akses masuk sering jadi titik awal penyalahgunaan sistem. Setiap percobaan login menyimpan data waktu, lokasi, dan status berhasil atau gagal, yang memberi gambaran kondisi sistem saat itu.

Peninjauan awal menunjukkan pola yang tidak biasa. Login terjadi di luar jam kerja dan beberapa datang dari lokasi yang jarang dipakai. Kondisi ini belum pasti jadi insiden, tapi cukup kuat untuk lanjut ke analisis lebih dalam.

Analisis difokuskan pakai filter SQL untuk mempersempit data. Perhatian diarahkan pada info paling relevan sebelum membuat query lebih detail. Alur ini membuat investigasi tetap rapi, terfokus, dan menyoroti risiko yang paling jelas dulu.

---

## 🕒 After hours failed login attempts <a name="failed">

Analisis dimulai dengan menyoroti percobaan login gagal di luar jam kerja. Pola ini biasanya muncul saat ada upaya akses tidak sah, karena sistem seharusnya jarang digunakan di waktu tersebut. Waktu akses jadi indikator penting karena aktivitas setelah jam operasional cenderung berisiko lebih tinggi.

Pola dikenali dengan memfilter tabel `log_in_attempts` menggunakan dua kriteria utama. Login dicatat setelah pukul 18.00 dan statusnya gagal. Operator `AND` dipakai supaya kedua kondisi ini muncul bersamaan dalam satu query.

<img width="645" height="426" alt="Screenshot 2026-01-21 at 23-23-27 Activity Filter with AND OR and NOT Google Skills" src="https://github.com/user-attachments/assets/c6df27e9-c0ea-4cc1-b21e-448fbc657e11" />

Hasil query menunjukkan 19 entri, semua login gagal terjadi malam hari hingga hampir tengah malam. Lokasi akses tersebar di beberapa negara seperti `US`, `CAN`, `MEXICO`, `USA`, dan `CANADA`, menandakan percobaan tidak datang dari satu wilayah saja.

Beberapa username muncul berulang dalam tanggal yang berdekatan, menandakan percobaan akses berulang. Pola waktu yang aneh, status gagal, dan variasi lokasi membuat data ini jadi prioritas investigasi. Temuan ini memberi dasar jelas untuk analisis lebih lanjut soal tanggal dan lokasi percobaan login.

---

## 📅 Login attempts on specific dates <a name="specificdate">

Analisis dilanjutkan dengan menyoroti tanggal yang memicu peringatan sistem. Tanggal 9 Mei 2022 menjadi fokus karena ada lonjakan aktivitas login. Data sehari sebelumnya juga dianalisis supaya konteks kejadian lebih jelas.

Pemfilteran dilakukan menggunakan SQL dengan logika `OR`. Dua tanggal dianalisis dalam satu query tanpa memisahkan alur data. Tujuannya melihat pola aktivitas secara menyeluruh, bukan cuma satu kejadian.

<img width="645" height="470" alt="Screenshot 2026-01-21 at 23-36-40 Activity Filter with AND OR and NOT Google Skills" src="https://github.com/user-attachments/assets/5f71a263-4dda-4595-9496-f494d8d4e648" />

Hasil query menunjukkan 75 baris data untuk kedua tanggal. Aktivitas tersebar dari dini hari sampai malam. Beberapa login berhasil, tapi cukup banyak yang gagal, menunjukkan percobaan akses tinggi dalam waktu singkat.

Beberapa username muncul berulang pada kedua tanggal dengan status login berbeda. Pola ini mengindikasikan percobaan akses berulang. Data lokasi memperlihatkan asal login dari berbagai negara, kadang formatnya tidak konsisten, menunjukkan sumber akses beragam.

Temuan ini jadi penghubung antara analisis waktu dan lokasi. Memahami aktivitas di tanggal tertentu membantu mempersempit fokus investigasi ke tahap berikut dengan dasar data yang lebih jelas dan relevan.

---

## 🌐 Login attempts outside authorized locations <a name="outside">

Analisis dilanjutkan dengan meninjau lokasi login. Aktivitas mencurigakan yang bukan berasal dari Meksiko menjadi fokus utama. Lokasi dijadikan indikator awal untuk melihat kemungkinan akses lintas negara yang tidak sesuai pola kerja normal.

Pemfilteran menggunakan SQL dengan kombinasi `NOT` dan `LIKE` pada kolom country. Penulisan nama negara kadang tidak konsisten, misalnya `MEX` dan `MEXICO`. Query ini tetap bisa mengelompokkan entri yang sama lalu mengecualikannya menggunakan `NOT`.

<img width="645" height="466" alt="Screenshot 2026-01-21 at 23-48-09 Activity Filter with AND OR and NOT Google Skills" src="https://github.com/user-attachments/assets/8318262b-2d0f-41fa-8d22-002a85e1e759" />

Hasil query menampilkan 144 baris data dari negara selain Meksiko. Aktivitas login muncul dari `US`, `USA`, `CAN`, dan `CANADA`. Mayoritas akses sistem memang terjadi dari luar wilayah yang sebelumnya dianggap aman.

Data menunjukkan kombinasi login berhasil dan gagal. Beberapa akun mencoba masuk berulang kali dari negara sama dalam waktu dekat. Pola ini mengindikasikan percobaan brute force ringan atau penggunaan kredensial tidak valid dari luar wilayah operasional utama.

Temuan ini memberi konteks risiko sebelumnya. Pengecualian Meksiko membantu mempersempit fokus investigasi pada sumber akses eksternal yang perlu dipantau lebih ketat pada tahap berikut.

## 👥 Employee segmentation for security updates <a name="securityupdate">

### a. Employees in marketing department

Analisis dilanjutkan dengan membagi karyawan ke dalam segmen untuk prioritas pembaruan keamanan. Fokusnya pada pengguna yang punya akses ke sistem internal dan perangkat yang sering digunakan. Data diambil dari tabel `employees` dengan dua filter, yaitu departemen dan lokasi kantor. Departemen Marketing dipilih karena banyak berinteraksi dengan pihak eksternal. Kantor yang namanya diawali East dijadikan indikator satu klaster lokasi fisik yang perlu diperiksa lebih detail.

Query ini memadukan `AND` dan `LIKE` untuk mencocokkan pola lokasi kantor:

<img width="645" height="234" alt="Screenshot 2026-01-22 at 00-01-20 Activity Filter with AND OR and NOT Google Skills" src="https://github.com/user-attachments/assets/5b5889a9-76c7-470f-be01-0b4737e1add4" />

Hasil query menunjukkan tujuh karyawan Marketing di kantor East. Data menampilkan `username` dan `device_id` yang terhubung ke sistem organisasi. Sebagian besar perangkat tercatat, tapi ada satu yang `NULL`, artinya perangkat itu belum terdaftar atau belum terhubung ke sistem manajemen aset. Perangkat tanpa identifikasi ini berisiko tidak menerima pembaruan keamanan atau kebijakan kontrol akses.

Segmentasi ini membantu tim keamanan menentukan target distribusi update dengan tepat. Identifikasi pengguna, departemen, dan lokasi membuat tindakan keamanan bisa dilakukan fokus tanpa mengganggu operasional organisasi secara keseluruhan.

### b. Employees in finance or sales department

Tahap segmentasi berikutnya fokus pada karyawan di departemen Finance dan Sales. Kedua tim ini dipilih karena sering mengakses data sensitif dan intensitas penggunaan sistemnya tinggi, jadi perlu diperhatikan dalam pembaruan keamanan dan kontrol akses. Pengelompokan dua departemen dilakukan sekaligus pakai operator `OR` supaya data bisa ditarik tanpa query terpisah.

Query SQL yang digunakan terlihat seperti ini:

<img width="645" height="406" alt="Screenshot 2026-01-22 at 00-15-10 Activity Filter with AND OR and NOT Google Skills" src="https://github.com/user-attachments/assets/8f594dcf-9cc9-41f6-9097-ac12e1e98433" />

Hasil query menampilkan 71 karyawan Finance dan Sales. Data mencakup `employee_id`, `username`, `department`, `office`, dan `device_id` yang penting untuk pengelolaan akun dan keamanan perangkat. Lokasi karyawan cukup tersebar, mencakup North, South, East, West, sampai Central. Situasi ini menunjukkan pembaruan keamanan harus direncanakan lintas wilayah, bukan cuma satu lokasi.

Beberapa `device_id` tercatat `NULL`, artinya ada perangkat yang belum terdaftar di sistem manajemen aset. Perangkat tanpa identitas berisiko tidak mendapatkan pembaruan atau kebijakan proteksi. Segmentasi pakai logika `OR` membantu tim keamanan menentukan prioritas dengan jelas. Pemetaan berdasarkan departemen kritis memungkinkan mitigasi dilakukan selektif dan terkendali tanpa mempengaruhi seluruh organisasi.

### c. Employees not in IT department

Tahap ini fokus pada segmentasi karyawan yang bukan dari IT menggunakan exclusion filtering. Tim IT biasanya punya jadwal dan mekanisme pembaruan sendiri, berbeda dengan departemen lain. Operator `NOT` di SQL digunakan supaya seluruh karyawan selain IT bisa ditarik sekaligus dalam satu query.

Query SQL yang dipakai seperti ini:

<img width="645" height="416" alt="Screenshot 2026-01-22 at 01-24-33 Activity Filter with AND OR and NOT Google Skills" src="https://github.com/user-attachments/assets/e40f9fab-221a-4608-90ff-c0677bedb34b" />

Hasilnya menunjukkan 161 karyawan non IT dari Marketing, Finance, Sales, dan Human Resources. Data menampilkan `employee_id`, `username`, `department`, `office`, dan `device_id` yang penting untuk memetakan cakupan pembaruan keamanan. Sebagian besar berada di luar IT dan tersebar di banyak lokasi kantor. Situasi ini menegaskan perlunya membedakan kebijakan security update antara tim teknis dan non teknis, khususnya soal waktu dan intervensi sistem.

Beberapa `device_id` masih `NULL`, menandakan perangkat belum tercatat di inventaris dan berisiko tidak menerima pembaruan otomatis. Exclusion filtering pakai `NOT` membuat segmentasi lebih strategis. Tim keamanan bisa fokus menyalurkan update ke kelompok relevan tanpa melibatkan IT untuk proses yang tidak diperlukan, menjaga efisiensi sekaligus keamanan.

---

## 📝 Final summary <a name="summary">

Studi kasus ini mencatat pengalaman melakukan investigasi keamanan pakai SQL dengan fokus pada analisis aktivitas login dan segmentasi karyawan di sebuah organisasi. Seluruh langkah dibuat sebagai bagian portofolio cybersecurity untuk menunjukkan kemampuan membaca data, menyaring informasi, dan menarik insight relevan dari perspektif keamanan sistem.

Investigasi dimulai dengan meninjau log login sebagai indikator awal potensi insiden. Analisis login gagal di luar jam kerja memperlihatkan pola akses tidak biasa yang perlu diperhatikan lebih lanjut. Operator `AND`, filter waktu, dan status login dipakai untuk mempersempit ruang lingkup investigasi secara efektif.

Tahap berikutnya menekankan penyaringan berdasarkan tanggal tertentu pakai logika `OR`, sehingga aktivitas pada periode spesifik bisa dianalisis tanpa kehilangan konteks historis. Pendekatan ini membantu memahami pola login dalam rentang waktu dekat dan mendukung analisis kronologis.

Selain log aktivitas, investigasi diperluas ke tabel karyawan untuk segmentasi pengguna. Penyaringan dilakukan berdasarkan departemen dan lokasi kantor pakai `AND` dan `LIKE`, pengelompokan lintas departemen pakai `OR`, dan pengecualian departemen tertentu pakai `NOT`. Setiap metode menunjukkan bagaimana SQL bisa mendukung pengambilan keputusan keamanan yang lebih terarah.

Keseluruhan portofolio ini menunjukkan pemahaman praktis penggunaan SQL untuk investigasi keamanan. Dokumentasi dibuat terstruktur dan mudah dipahami, mencerminkan kemampuan analisis data, penalaran keamanan, dan kesiapan menjelaskan proses teknis dalam konteks profesional maupun kebutuhan rekrutmen di bidang cybersecurity.
