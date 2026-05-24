# 📘 RAG Parameter Exploration Assignment

Pada tugas ini kamu akan bereksperimen dengan **6 parameter utama Retrieval-Augmented Generation (RAG)** menggunakan **RAG Playground**.

### 🎯 Tujuan Pembelajaran

Kamu akan:

✅ Memahami **apa fungsi** masing-masing parameter RAG

✅ Mengamati **pengaruh** tuning terhadap kualitas jawaban vs kecepatan

✅ Belajar **trade-off** antara retrieval & generation

✅ Menulis **insight praktis** dari eksperimen yang kamu lakukan

> **Tools sudah disediakan.** Kamu **tidak perlu ngoding**, hanya ubah nilai parameter di file `app.py` & restart server.

### ⚠️ Notes Penting

- **Ikuti video di LMS jika kamu kebingungan.**
- **Sediakan penyimpanan at least 2GB**, akan ada download model yang cukup besar ke laptopmu.
- **Hapus proyek ini kalau sudah selesai**, biar laptop-mu tidak tercekik.
- **Ketika proyek ini sudah diisi & dikerjakan di bawah, upload ke GitHub.**

---

## ⚙️ Parameter yang Diuji

Semua parameter berikut dapat ditemukan di file `app.py`:

```python
# Buka app.py, cari bagian KONFIGURASI INTI
CHUNK_SIZE = 300
TOP_K_INITIAL = 20
FINAL_TOP_M = 5
USE_RERANKER = True
TEMPERATURE = 0.2
GROQ_MODEL = "llama-3.1-8b-instant"
```

---

### 📏 1. CHUNK_SIZE

```python
CHUNK_SIZE = 300  # ubah angka ini
```

**Apa itu CHUNK_SIZE?**

Saat dokumen PDF diupload, teksnya dipotong-potong menjadi bagian kecil (chunk) agar bisa diproses dan dicari. `CHUNK_SIZE` menentukan berapa banyak **kata** per potongan.

**Fungsi:** *(Jelaskan dengan kata-katamu sendiri apa fungsi dari CHUNK_SIZE)*

> Jawab: Chunk Size berfungsi untuk mengubah informasi berupa teks yang diproses oleh model menjadi bagian-bagian kecil agar model RAG tidak kesulitan memproses informasi, informasi telah dibagi-bagi sehingga model tinggal mencari informasi yang diinginkan pada bagian-bagian yang telah dibagi tersebut secara lebih cepat.

**Pengaruhnya:**

| Range Nilai | Efek |
| --- | --- |
| **Kecil (100–200)** | *Ketika chunk size diubah menjadi lebih kecil, misal 150, maka teks dibagi menjadi 8 bagian yang terbilang banyak, sehingga waktu kerja proses menjadi sedikit lebih lambat. Jawaban yang diberikan pun lebih singkat dengan banyak konteks* |
| **Sedang (250–400)** ⭐ Recommended | *Ketika chunk size diubah ke range sedang, jawaban yang diberikan oleh model jauh lebih cepat. Teks dibagi menjadi 3-4 bagian saja sehingga model bisa memproses jawaban dengan lebih cepat dan jawaban yang diberikan pun lebih lengkap dan sesuai, sehingga direkomendasikan.* |
| **Besar (500–1000)** | *Chunk size yang besar membuat sehingga teks yang ada pun dibagi menjadi 2 bahkan tidak dibagi sama sekali. Jawaban yang dihasilkan model pun lebih panjang karena teks hampir tidak dibagi sama sekali dan konteks yang diberikan pun kadang tercampur dan tidak spesifik sesuai konteks pertanyaan.* |

**Pertanyaan Refleksi:**

* Chunk besar atau kecil yang lebih tepat untuk dokumen yang kamu tes? Mengapa?
  > Jawab: Chunk kecil lebih sesuai untuk dokumen yang saya tes karena menghasilkan jawaban yang lebih singkat dan padat sesuai dengan konteks pertanyaan, walaupun kadang terpotong. Dibandingkan dengan chunk besar yang malah memberikan jawaban yang kadang lebih dari apa yang ditanyakan/diluar konteks pertanyaan.

* Apa yang terjadi kalau jawaban terasa "terpotong" atau kurang konteks? Parameter mana yang harus diubah?
  > Jawab: Kalau jawaban terasa terpotong atau kurang konteks maka parameter yang harus diubah adalah ukuran chunk. Ketika ukuran chunk diperbesar maka jawaban yang dihasilkan model pun akan jauh lebih lengkap dan sesuai dengan konteks pertanyaan.
---

### 🔍 2. TOP_K_INITIAL

```python
TOP_K_INITIAL = 20
```

**Apa itu TOP_K_INITIAL?**

Setelah pertanyaan diubah jadi vektor, sistem mencari chunk yang paling mirip. `TOP_K_INITIAL` menentukan berapa banyak **kandidat awal** yang diambil dari hasil pencarian vektor sebelum di-filter/rerank.

**Fungsi:** *(Jelaskan dengan kata-katamu sendiri apa fungsi dari TOP_K_INITIAL)*

> Jawab: Top_K_Initial berfungsi untuk menentukan banyaknya kandidat chunk awal berdasarkan kemiripan vektornya. Chunk dokumen akan diubah menjadi angka vektor kemudian dibandingkan seberapa relevan jawabannya berdasarkan kemiripan angka vektor dari pertanyaan yang diberikan.

**Pengaruhnya:**

| Range Nilai | Efek |
| --- | --- |
| **Kecil (5–10)** | *Nilai kecil membuat sehingga prosesnya menjadi lebih cepat, akan tetapi konteksnya menjadi kurang relevan dikarenakan sedikitnya kandidat yang terpilih*
| **Sedang (15–25)** ⭐ Recommended | *Nilai sedang direkomendasikan karena kandidat yang terpilih cukup sehingga kecepatan proses dan relevansinya pun lebih baik.* |
| **Besar (30–50)** | *(Nilai besar menghasilkan banyak kandidat yang lebih seusai dan relevan, akan tetapi hal ini membuat sehingga waktu proses menjadi lebih lambat.)* |

> 💡 **Catatan:** Nilai ini hanya berpengaruh signifikan jika `USE_RERANKER = True`. Tanpa reranker, yang dipakai langsung hanya `FINAL_TOP_M` teratas.

**Pertanyaan Refleksi:**

* Apakah top-k kecil (5–10) cukup untuk dataset kamu? Jelaskan alasannya.
  > Jawab: Kurang sesuai dikarenakan dataset saya terdiri dari 3 poin utama yang dimana apabila nilai top-k kecil maka tidak semua kandidat dari ketiga poin tersebut akan terambil, sehingga hasilnya menjadi kurang relevan.

* Apa hubungan antara `TOP_K_INITIAL` dan `USE_RERANKER`?
  > Jawab: Top_K_Initial menentukan kandidat awal, sementara Use_Reranker memilih ulang kandidat terbaik dan paling relevan dari kandidat-kandidat yang telah ditentukan sebelumnya.

---

### 📋 3. FINAL_TOP_M

```python
FINAL_TOP_M = 5
```

**Apa itu FINAL_TOP_M?**

Dari sekian banyak kandidat yang ditemukan, hanya **M teratas** (setelah reranking) yang dikirim ke LLM sebagai konteks untuk menjawab pertanyaan.

**Fungsi:** *(Jelaskan dengan kata-katamu sendiri apa fungsi dari FINAL_TOP_M)*

> Jawab: Menentukan kandidat-kandidat terbaik yang akhirnya dikirim ke LLM sebagai konteks jawaban yang paling relevan setelah proses retrieval dan reranking dilakukan.

**Pengaruhnya:**

| Range Nilai | Efek |
| --- | --- |
| **Sedikit (2–3)** | *Nilai yang sedikit menghasilkan jawaban yang sangat fokus, akan tetapi relevansi jawabannya masih kurang.* |
| **Sedang (4–6)** ⭐ Recommended | *Range ini direkomendasikan karena menghasilkan jawaban yang fokusnya cukup dan isinya lengkap serta sesuai.* |
| **Banyak (7–10)** | *Range ini membuat jawaban yang dihasilkan sangat lengkap, akan tetapi karena konteks yang luas dan lengkap, hal ini dapat membuat model menjadi bingung karena panjangnya konteks tersebut.* |

> 💡 **Catatan:** Semakin banyak konteks = semakin banyak token yang dikirim ke LLM = latency lebih tinggi. Jaga agar tetap efisien.

**Pertanyaan Refleksi:**

* Benarkah "semakin banyak konteks semakin bagus"? Jelaskan berdasarkan eksperimenmu.
  > Jawab: Tidak selalu benar karena banyaknya konteks dapat membuat model kesulitan memilih informasi yang paling relevan karena fokusnya terbagi karena banyaknya konteks tersebut, sehingga jawaban yang dihasilkan menjadi kurang fokus.

* Apa yang terjadi kalau konteks yang dikirim ke LLM saling bertentangan?
  > Jawab: Hal ini dapat membuat jawaban yang dihasilkan tercampur menjadi jawaban yang tidak konsisten, sehingga relevansi jawabannya pun manjadi menurun.

---

### 🎯 4. USE_RERANKER

```python
USE_RERANKER = True  # atau False
```

**Apa itu Reranker?**

Reranker adalah model tambahan (Cross-Encoder) yang mengevaluasi ulang pasangan `[pertanyaan, chunk]` untuk mengurutkan relevansi lebih akurat daripada hanya cosine similarity biasa.

**Fungsi:** *(Jelaskan dengan kata-katamu sendiri apa fungsi dari USE_RERANKER)*

> Jawab: Fungsinya menilai ulang tingkat relevansi chunk dan pertanyaan sehingga hasil yang didapatkan oleh LLM menjadi lebih sesuai dan tepat.

**Pengaruhnya:**

| Mode | Efek |
| --- | --- |
| `True` | *Mode ini memberikan hasil yang lebih akurat dan relevan, akan tetapi prosesnya menjadi lebih lambat.* |
| `False` | *Mode ini menghasilkan proses yang lebih cepat, akan tetapi tingkat keakuratannya menjadi menurun.* |

> 💡 **Catatan:** Untuk dokumen teknis atau pertanyaan yang ambigu, reranker sangat membantu. Untuk kecepatan maksimal, matikan.

**Pertanyaan Refleksi:**

* Pada query jenis apa reranker paling terasa manfaatnya? Berikan contoh.
  > Jawab: Reranker paling terasa pada query yang sulit dimengerti dan ambigu, seperti "perbedaan antara sejarah dan perkembangan AI" atau "manfaat AI dalam bisnis dan bidang kecantika"

* Berapa kira-kira tambahan latency saat reranker dinyalakan di eksperimenmu?
  > Jawab: Saat reranker dinyalakan terdapat penambahan latency sebesar 100-300 ms

---

### 🌡️ 5. TEMPERATURE

```python
TEMPERATURE = 0.2  # range: 0.0 - 1.0
```

**Apa itu Temperature?**

Temperature mengatur distribusi probabilitas saat LLM memilih kata berikutnya. Semakin rendah, semakin "pasti" dan konsisten jawabannya.

**Fungsi:** *(Jelaskan dengan kata-katamu sendiri apa fungsi dari TEMPERATURE)*

> Jawab: Fungsi dari temperatur ialah mengatur seberapa besar keberagaman daripada jawaban yang dihasilkan oleh model, dimana semakin rendah nilainya maka jawaban yang dihasilkan pun semakin sesuai dengan informasi yang ada dan sebaliknya.

**Pengaruhnya:**

| Range Nilai | Efek |
| --- | --- |
| **0.0–0.2** ⭐ Recommended untuk RAG | *Jawaban yang diberikan sangat stabil dan faktual sehingga cocok digunakan untuk RAG* |
| **0.3–0.5** | *Jawaban yang dihasilkan masih akurat, akan tetapi sudah mulai bervariasi* |
| **0.6–0.8** | *Jawaban di range ini sudah kreatif dan bervariasi, sehingga hasilnya menjadi kurang stabil* |
| **0.9–1.0** | *Jawaban yang dihasilkan di range ini sudah sangat kreatif dan bisa keluar daripada konteks/halusinasi* |

> ⚠️ **Catatan:** Untuk RAG (jawaban berdasarkan dokumen), gunakan temperature rendah (0.1–0.3) agar jawaban tetap *grounded* pada konteks yang diberikan.

**Pertanyaan Refleksi:**

* Untuk QA berbasis dokumen perusahaan, temperatur ideal berapa? Jelaskan alasannya.
  > Jawab: 0.1 – 0.3 karena QA dokumen membutuhkan jawaban yang akurat dan berbasis konteks.

* Apa yang terjadi pada jawaban saat kamu naikkan temperature ke 0.9?
  > Jawab: Jawaban menjadi sangat kreatif dan bahkan isinya sudah menyimpang dari informasi yang ada di dokumen.

---

### 🤖 6. GROQ_MODEL

```python
GROQ_MODEL = "llama-3.1-8b-instant"  # model default
```

**Model lain yang bisa dicoba:**

```python
# Ganti value GROQ_MODEL ke salah satu di bawah:
GROQ_MODEL = "openai/gpt-oss-120b"
GROQ_MODEL = "llama-3.3-70b-versatile"
```

**Apa bedanya tiap model?**

Setiap model punya ukuran parameter dan kemampuan yang berbeda. Isi tabel di bawah berdasarkan eksperimenmu:

| Model | Ukuran | Pengaruh terhadap Kecepatan & Kualitas |
| --- | --- | --- |
| `llama-3.1-8b-instant` (Default) | 8B parameter | *Model ini bekerja dengan cepat dan menghasilkan jawaban yang cukup relevan dengan konteks. Model ini cocok digunakan untuk testing.* |
| `openai/gpt-oss-120b` | 120B parameter | *Jawaban yang dihasilkan model ini sangat akurat, tetapi model ini bekerja cukup lambat. Cocok digunakan untuk dokumen yang panjang dan kompleks.* |
| `llama-3.3-70b-versatile` | 70B parameter | *Model ini bekerja dengan sangat stabil dan cepat, sehingga cocok digunakan untuk berbagai jenis dokumen dan dataset, sesuai jika digunakan untuk membuat chatbot* |

> 💡 **Tips:** Mulai dengan `llama-3.1-8b-instant` untuk development. Saat butuh akurasi tinggi, switch ke `llama-3.3-70b-versatile` atau `openai/gpt-oss-120b`.

**Pertanyaan Refleksi:**

* Apakah peningkatan kualitas jawaban dari model besar sebanding dengan penambahan waktu? Jelaskan.
  > Jawab: Tidak selalu sebanding. Model besar memang cocok untuk data yang kompleks dan dapat menghasilkan jawaban yang akurat, akan tetapi ini kurang sesuai apabila digunakan untuk dataset yang kecil. Model yang lebih kecil dan sederhana lebih cocok untuk bekerja pada dataset yang kecil karena dapat menghasilkan jawaban yang sesuai dalam waktu yang singkat.

* Model mana yang paling cocok untuk use-case dokumenmu? Mengapa?
  > Jawab: Model yang paling cocok adalah llama-3.1-8b-instant, karena dokumen saya hanya terdiri dari 3 halaman dan informasi yang terdapat didalamnya pun tidak terlalu banyak dan kompleks, sehingga model ini sudah cukup untuk menghasilkan jawaban yang sesuai dalam waktu yang singkat.

---

## ⚙️ Parameter Tambahan (Non-Tunable)

Parameter ini sudah di-set optimal. Ubah hanya jika paham dampaknya.

```python
CHUNK_OVERLAP = 60     # overlap antar chunk (kata)
SCORE_THRESHOLD = 0.30 # skor minimum cosine similarity
```

**Penjelasan:**

| Parameter | Nilai Default | Fungsi |
| --- | --- | --- |
| `CHUNK_OVERLAP` | 60 | *Fungsi overlap antar chunk ialah menjaga kesinambungan konteks informasi di batas akhir chunk pada saat dibagi atau dipotong, sehingga informasinya menjadi lebih konsisten. Ketika overlap terlalu kecil maka konteks antar chunk akan menjadi terpotong sehingga konteksnya informasinya menjadi kurang sesuai, sebaliknya apabila overlap terlalu besar maka banyak isi chunk menjadi berulang sehingga informasi yang dihasilkan pun menjadi berulang.* |
| `SCORE_THRESHOLD` | 0.30 | *Threshold skor berfungsi sebagai batas minimum skor similarity antara query dan chunk sebelum chunk dianggap relevan untuk digunakan sebagai konteks. Ketika parameter ini dinaikan maka sistem menjadi lebih selektif sehingga hanya menghasilkan konteks yang relevan, tetapi bisa juga menghilangkan konteks lainnya yang tidak kalah penting. Sebaliknya, ketika diturunkan maka banyak chunk yang lolos, akan tetapi hal ini berarti juga bahwa konteks yang kurang relevan juga dapat ikut masuk sehingga kualitas jawaban menjadi menurun.* |

---

## 📝 Tips Eksperimen

* Pakai PDF minimal 1 halaman, maksimal 10 halaman, biar gak berat
* Coba berbagai jenis pertanyaan: **definisi**, **konteks**, dan **reasoning**
* Catat kapan sistem salah & mengapa
* Ubah **satu parameter pada satu waktu** agar bisa melihat pengaruhnya secara jelas
* Prioritaskan kualitas insight dalam laporan

---

## 🔗 Referensi

* Dokumentasi lengkap parameter: buka `docs.html` di browser atau akses via menu **📖 Dokumentasi** di RAG Playground
* Source code: `app.py` — semua parameter ada di bagian `KONFIGURASI INTI`
