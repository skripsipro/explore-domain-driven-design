
# Domain-Driven Design (DDD)

## 1. Apa Itu DDD?
**Domain-Driven Design (DDD)** adalah pendekatan desain perangkat lunak yang menjadikan **domain bisnis** sebagai inti dari aplikasi. Tujuan utamanya adalah memastikan bahwa perangkat lunak mencerminkan kebutuhan bisnis dengan jelas dan mudah dipahami oleh semua pihak, termasuk developer dan pemangku kepentingan.

---

## 2. Konsep Utama DDD

### **Fokus pada Domain Bisnis**
- **Domain**: Segala hal yang berkaitan dengan kebutuhan bisnis.
- Dalam DDD, kita mempelajari bagaimana bisnis bekerja, aturan yang harus diikuti, dan prosesnya.

**Contoh:**
- Dalam sistem perekrutan:
  - Kandidat melamar pekerjaan.
  - HR menilai kandidat.
  - Kandidat diterima atau ditolak.

---

### **Bahasa Seragam (Ubiquitous Language)**
- Semua orang (developer, pemangku kepentingan, analis) harus menggunakan **bahasa yang sama** untuk berbicara tentang domain.
- Bahasa ini diterjemahkan langsung ke dalam kode.

**Contoh:**
- Pemangku kepentingan berbicara tentang "Kandidat" dan "Lamaran".
- Dalam kode, Anda juga menggunakan istilah `Kandidat` dan `Lamaran`, bukan nama generik seperti `User` atau `Application`.

---

### **Membagi Domain ke Sub-Domain**
- Domain besar sering kali terlalu kompleks, sehingga dipecah menjadi **bounded contexts** (sub-domain).
- Setiap sub-domain memiliki batas yang jelas, dengan logika bisnis dan data sendiri.

**Contoh:**
- Dalam sistem perekrutan:
  - **Sub-Domain Kandidat**: Mengelola data kandidat.
  - **Sub-Domain Penilaian**: Menilai kandidat berdasarkan kriteria.
  - **Sub-Domain Keputusan**: Memutuskan apakah kandidat diterima atau ditolak.

---

### **Komponen Utama DDD**

1. **Entities**:
   - Objek dengan identitas unik.
   - Tetap sama walaupun nilainya berubah.
   - **Contoh**: `Kandidat` dengan ID unik.

2. **Value Objects**:
   - Objek tanpa identitas unik, hanya berisi nilai.
   - **Contoh**: `Alamat` kandidat.

3. **Aggregates**:
   - Kumpulan entities dan value objects yang menjadi satu unit logis.
   - **Contoh**: `Kandidat` dengan `Lamaran` sebagai bagian dari agregat.

4. **Repositories**:
   - Abstraksi untuk menyimpan dan mengambil entities atau aggregates.
   - **Contoh**: `KandidatRepository` untuk mengambil data kandidat.

5. **Domain Services**:
   - Logika bisnis yang tidak cocok masuk ke entities atau value objects.
   - **Contoh**: Menghitung skor kandidat berdasarkan beberapa kriteria.

6. **Domain Events**:
   - Mewakili sesuatu yang terjadi di domain.
   - **Contoh**: Event "Kandidat Melamar".

---

## 3. Memisahkan Logika Bisnis dan Infrastruktur
- Infrastruktur seperti database, API, dan server tidak boleh bercampur dengan logika domain.
- DDD memisahkan logika bisnis (domain) dari detail teknis (infrastruktur).

**Contoh:**
- Dalam DDD, Anda tidak mengakses database langsung di kode domain. Anda menggunakan repository untuk menjembatani domain dengan database.

---

## 4. Alur Kerja DDD
1. **Pengguna** melakukan suatu tindakan.
2. Permintaan diproses di lapisan aplikasi (use case).
3. Lapisan aplikasi meminta logika bisnis dari domain layer (entities, services).
4. Hasil dikirim kembali ke infrastruktur layer untuk ditampilkan kepada pengguna.

---

## 5. Mengapa Menggunakan DDD?
- **Cocok untuk Proyek yang Kompleks**:
  - Jika bisnis memiliki banyak aturan dan relasi.
- **Meningkatkan Pemahaman**:
  - Semua tim menggunakan bahasa yang sama, sehingga komunikasi lebih efektif.
- **Modular dan Mudah Diperluas**:
  - Dengan memisahkan domain dari infrastruktur, kode menjadi lebih mudah dirawat dan diperluas.

---

## 6. Kapan Tidak Perlu Menggunakan DDD?
- Jika proyek sederhana (hanya CRUD).
- Jika logika bisnis sangat minimal.
- Jika pengembangan cepat lebih penting daripada skalabilitas.

---

## 7. Kesimpulan
- **DDD** adalah pendekatan untuk membangun perangkat lunak **berbasis domain bisnis**.
- **Fokus DDD**: Logika bisnis harus mencerminkan kebutuhan domain, bukan teknis.
- DDD membantu Anda membuat perangkat lunak yang dapat diandalkan, skalabel, dan mencerminkan bagaimana bisnis benar-benar bekerja.

