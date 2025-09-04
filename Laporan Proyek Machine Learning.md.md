
# Laporan Proyek Machine Learning - Sistem Rekomendasi Film - Wayan Ardike
![](https://c.files.bbci.co.uk/D1E0/production/_106082735_mediaitem106082731.jpg)

## Project Overview

Proyek ini berfokus pada pengembangan sistem rekomendasi film menggunakan dataset `ml-latest-small` dari MovieLens. Dalam era digital saat ini, di mana pengguna dihadapkan pada ribuan pilihan film dari berbagai platform _streaming_, kemampuan untuk menemukan konten yang relevan dan menarik menjadi krusial. Masalah utama yang ingin diatasi adalah _oversaturation_ informasi, di mana pengguna sering kali kesulitan memilih film yang sesuai dengan preferensi mereka, yang dapat menyebabkan _decision fatigue_ atau bahkan kehilangan minat.

Sistem rekomendasi menjadi solusi vital untuk masalah ini karena dapat mempersonalisasi pengalaman pengguna dengan menyarankan film berdasarkan riwayat tontonan, rating, atau preferensi genre mereka. Dengan demikian, sistem ini tidak hanya meningkatkan kepuasan pengguna tetapi juga mendorong eksplorasi konten baru yang mungkin tidak ditemukan secara manual.

Proyek ini mengadopsi pendekatan _Content-Based Filtering_, yang berprinsip pada rekomendasi item yang serupa dengan item yang disukai pengguna di masa lalu. Pendekatan ini dipilih karena kemampuannya untuk memberikan rekomendasi yang sangat personal dan relevan dengan preferensi eksplisit pengguna, serta tidak memerlukan data dari pengguna lain (masalah _cold start_ untuk pengguna baru lebih mudah diatasi dibandingkan _collaborative filtering_).

**Referensi:**
Salim, E., Pragantha, J., & Lauro, M. D. _Perancangan Sistem Rekomendasi Film menggunakan metode Content-based Filtering_. Jurusan Teknik Informatika, Fakultas Teknologi Informasi, Universitas Tarumanagara. Diambil dari [https://lintar.untar.ac.id](https://lintar.untar.ac.id/repository/penelitian/buktipenelitian_10390001_7A281222103549.pdf)
    

## Business Understanding

Bagian ini menguraikan identifikasi masalah dan tujuan yang ingin dicapai melalui proyek sistem rekomendasi film.

### Problem Statements

1.  **Kesulitan Penemuan Konten Relevan:** Pengguna seringkali kesulitan menemukan film yang benar-benar sesuai dengan selera mereka di antara jutaan pilihan yang tersedia, menyebabkan pengalaman menonton yang kurang memuaskan.
    
2.  **Pemanfaatan Data Metadata Film:** Bagaimana cara efektif memanfaatkan metadata film (genre, tag) untuk membangun model rekomendasi yang akurat dan personal?
    
3.  **Evaluasi Kualitas Rekomendasi:** Bagaimana cara mengukur seberapa baik rekomendasi yang diberikan oleh sistem dalam memenuhi preferensi pengguna?
    

### Goals

1.  **Meningkatkan Kepuasan Pengguna:** Menyediakan daftar rekomendasi film yang relevan dan menarik bagi pengguna, sehingga meningkatkan kepuasan dan keterlibatan mereka dengan platform film.
    
2.  **Membangun Sistem Rekomendasi Berbasis Konten:** Mengembangkan model _Content-Based Filtering_ yang mampu mengidentifikasi kesamaan antarfilm berdasarkan genre, lalu merekomendasikan film yang paling mirip.
    
3.  **Mengukur Akurasi Rekomendasi:** Mengevaluasi kinerja sistem rekomendasi menggunakan metrik _Precision_ untuk memastikan bahwa rekomendasi yang dihasilkan memiliki tingkat relevansi yang tinggi.
    

## Data Understanding

Dataset yang digunakan dalam proyek ini adalah **ml-latest-small**, yang merupakan kumpulan data aktivitas rating bintang 5 dan _free-text tagging_ dari layanan rekomendasi film MovieLens. Dataset ini dirilis pada 26 September 2018 dan dirancang sebagai dataset pengembangan.

**Sumber Dataset:**  [MovieLens (ml-latest-small)](https://files.grouplens.org/datasets/movielens/ml-latest-small.zip "null")

Dataset ini terdiri dari empat file CSV: `links.csv`, `movies.csv`, `ratings.csv`, dan `tags.csv`.

**Informasi Umum Data:**

-   **Jumlah Rating:** 100.836 rating
    
-   **Jumlah Aplikasi Tag:** 3.683 tag
    
-   **Jumlah Film Unik:** 9.742 film
    
-   **Jumlah Pengguna Unik:** 610 pengguna
    
-   **Rentang Waktu Data:** 29 Maret 1996 hingga 24 September 2018.
    

**Variabel-variabel pada dataset:**

1.  **`links.csv`**: Berisi ID untuk menghubungkan film ke sumber data eksternal.
    
    -   `movieId`: ID unik untuk setiap film dalam dataset MovieLens. (Integer)
        
    -   `imdbId`: ID film pada basis data IMDb. (Integer)
        
    -   `tmdbId`: ID film pada basis data TMDb (The Movie Database). (Float, terdapat _missing value_)
        
2.  **`movies.csv`**: Berisi informasi dasar tentang film.
    
    -   `movieId`: ID unik untuk setiap film. (Integer)
        
    -   `title`: Judul film, termasuk tahun rilis dalam tanda kurung. (Object/String)
        
    -   `genres`: Genre atau kategori film, dipisahkan oleh tanda `|`. (Object/String)
        
3.  **`ratings.csv`**: Berisi data rating yang diberikan oleh pengguna.
    
    -   `userId`: ID unik pengguna yang memberikan rating. (Integer)
        
    -   `movieId`: ID film yang diberikan rating. (Integer)
        
    -   `rating`: Nilai rating yang diberikan pengguna terhadap film, pada skala 0.5 hingga 5.0 bintang. (Float)
        
    -   `timestamp`: Waktu saat rating diberikan (detik sejak 1 Januari 1970 UTC). (Integer)
        
4.  **`tags.csv`**: Berisi tag yang diberikan pengguna pada film.
    
    -   `userId`: ID pengguna yang memberikan tag. (Integer)
        
    -   `movieId`: ID film yang ditandai. (Integer)
        
    -   `tag`: Kata kunci atau label yang diberikan oleh pengguna. (Object/String)
        
    -   `timestamp`: Waktu pemberian tag. (Integer)
        

**Insight dari Exploratory Data Analysis (EDA):**

-   **Jumlah Data:** Setelah menggabungkan `movieId` dari semua file, terdapat **9742 film unik**. Sementara itu, setelah menggabungkan `userId` dari `ratings.csv` dan `tags.csv`, ditemukan **610 pengguna unik**.
    
-   **Distribusi Rating:** Rata-rata rating adalah sekitar 3.5, dengan sebagian besar rating berkisar antara 3.0 hingga 4.0. Ini menunjukkan bahwa pengguna cenderung memberikan penilaian positif hingga netral.
    
-   **Missing Values:** Setelah penggabungan awal (`movie` DataFrame), terdapat banyak _missing value_ pada kolom `imdbId`, `tmdbId`, `title`, `genres`, `userId_y` (dari `tags`), `tag`, dan `timestamp_y` (dari `tags`). Hal ini wajar karena tidak semua film memiliki tag atau tidak semua rating memiliki informasi lengkap dari semua file. Khususnya, kolom `tag` memiliki 52.549 _missing value_ setelah penggabungan `all_movie_name` dengan `tags`.
    
-   **Jumlah Unik Setelah Pembersihan:** Setelah pembersihan dan penghapusan duplikat, jumlah film unik yang digunakan untuk pemodelan adalah **1554**, dengan 10 jenis rating unik, 370 genre unik, dan 1584 tag unik.
    

## Data Preparation

Tahapan _Data Preparation_ dilakukan untuk membersihkan, mengintegrasikan, dan mempersiapkan data agar sesuai untuk pemodelan sistem rekomendasi.

1.  **Penggabungan Data Awal (`movie_all`, `user_all`):**
    
    -   **Proses:** Menggabungkan semua `movieId` unik dari `links.csv`, `movies.csv`, `ratings.csv`, dan `tags.csv` ke dalam satu array `movie_all`. Hal serupa dilakukan untuk `userId` dari `ratings.csv` dan `tags.csv` ke dalam `user_all`.
        
    -   **Alasan:** Untuk mendapatkan daftar lengkap film dan pengguna yang ada di seluruh dataset, memastikan tidak ada ID yang terlewat.
        
2.  **Penggabungan Informasi Film dan Rating (`movie`, `all_movie_rate`, `all_movie_name`, `all_movie`):**
    
    -   **Proses:**
        
        -   `movie_info = pd.concat([links, movies, tags])`: Menggabungkan `links`, `movies`, dan `tags` untuk mengumpulkan semua metadata film.
            
        -   `movie = pd.merge(ratings, movie_info, on='movieId', how='left')`: Menggabungkan data rating dengan semua metadata film berdasarkan `movieId`. Ini menghasilkan DataFrame yang sangat besar dengan banyak _missing value_ karena sifat penggabungan.
            
        -   `all_movie_rate = ratings[['userId', 'movieId', 'rating']]`: Memilih kolom `userId`, `movieId`, `rating` dari DataFrame `ratings`.
            
        -   `all_movie_name = pd.merge(all_movie_rate, movies[['movieId','title', 'genres']], on='movieId', how='left')`: Menggabungkan rating dengan informasi judul dan genre film.
            
        -   `all_movie = pd.merge(all_movie_name, tags[['movieId','tag']], on='movieId', how='left')`: Menggabungkan hasil sebelumnya dengan informasi tag film.
            
    -   **Alasan:** Tujuan utama adalah mengkonsolidasi semua informasi yang relevan (pengguna, rating, judul, genre, tag) ke dalam satu DataFrame untuk analisis dan pemodelan yang lebih mudah.
        
3.  **Penanganan Missing Value (`all_movie_clean`):**
    
    -   **Proses:** Menggunakan `all_movie.dropna()` untuk menghapus baris yang memiliki nilai `NaN` (Not a Number) atau _missing value_.
        
    -   **Alasan:** Setelah penggabungan, kolom `tag` memiliki 52.549 _missing value_. Menghapus baris dengan _missing value_ ini memastikan bahwa semua entri yang digunakan untuk pemodelan memiliki informasi genre dan tag yang lengkap, yang krusial untuk _Content-Based Filtering_. Hasilnya, data bersih berjumlah 233.213 baris.
        
4.  **Penghapusan Duplikat dan Finalisasi Data (`fix_movie`, `movie_new`):**
    
    -   **Proses:**
        
        -   `fix_movie = all_movie_clean.sort_values('movieId', ascending=True)`: Mengurutkan data yang sudah bersih berdasarkan `movieId`.
            
        -   `preparation = fix_movie.drop_duplicates('movieId')`: Menghapus baris duplikat berdasarkan `movieId`. Ini penting karena satu film bisa memiliki banyak entri rating atau tag dari pengguna yang berbeda, tetapi untuk tujuan rekomendasi berbasis konten (berdasarkan atribut film), kita hanya membutuhkan satu entri unik per film. Hasilnya, `preparation` memiliki 1554 baris unik film.
            
        -   Mengonversi kolom `movieId`, `title`, `genre`, dan `tag` dari `preparation` menjadi list terpisah.
            
        -   `movie_new = pd.DataFrame({'id': movie_id, 'title': movie_title, 'genre': movie_genre, 'tag': movie_tag})`: Membuat DataFrame final `movie_new` yang berisi ID, judul, genre, dan tag unik untuk setiap film.
            
    -   **Alasan:** Langkah ini memastikan setiap film direpresentasikan sekali dengan atributnya yang relevan, mencegah bias dalam perhitungan kesamaan dan mempersiapkan data dalam format yang optimal untuk TF-IDF Vectorizer.

5.  **Vektorisasi Fitur dengan TF-IDF:**
    
    -   **Proses:** Menggunakan teknik TF-IDF (Term Frequency-Inverse Document Frequency) untuk mengubah teks deskriptif (genre film) menjadi representasi numerik. `TfidfVectorizer` dari scikit-learn digunakan untuk menghitung nilai TF-IDF untuk setiap kata (genre) dalam setiap dokumen (film).
        
    -   **Alasan:** TF-IDF adalah teknik penting dalam pemrosesan bahasa alami (NLP) yang membantu mengukur seberapa penting sebuah kata dalam suatu dokumen relatif terhadap korpus dokumen. Dalam konteks ini, TF-IDF memungkinkan sistem untuk memahami relevansi setiap genre untuk setiap film, yang kemudian digunakan untuk menghitung kesamaan antarfilm. Ini merupakan langkah krusial dalam mengubah data tekstual menjadi format yang dapat digunakan oleh algoritma _machine learning_.
        

## Modeling

Tahapan ini membahas pengembangan model sistem rekomendasi menggunakan pendekatan _Content Based Filtering_.

### Content Based Filtering

Model ini bekerja dengan menganalisis atribut-atribut film untuk menemukan kesamaan antarfilm. Prosesnya melibatkan dua langkah utama:

#### 1. Cosine Similarity

-   **Tujuan:** Mengukur tingkat kesamaan antara dua film berdasarkan vektor TF-IDF genre mereka.
    
-   **Proses:**
    
    -   `cosine_sim = cosine_similarity(tfidf_matrix)`: Menghitung _cosine similarity_ antara semua pasangan film dalam `tfidf_matrix`. Hasilnya adalah matriks similiaritas di mana setiap sel `(i, j)` berisi nilai kesamaan antara film `i` dan film `j`.
        
    -   `cosine_sim_df = pd.DataFrame(cosine_sim, index=data['title'], columns=data['title'])`: Mengubah matriks _cosine similarity_ menjadi DataFrame dengan judul film sebagai indeks dan kolom, memudahkan interpretasi.
        
-   **Output:**  `cosine_sim_df.shape` adalah `(1554, 1554)`, menunjukkan matriks kesamaan antara 1554 film.
    
    -   **Interpretasi Nilai Cosine Similarity:**
        
        -   Nilai **1.0** menunjukkan kesamaan yang sempurna antara dua film (misalnya, film yang sama atau film dengan kombinasi genre yang persis sama).
            
        -   Nilai mendekati **0** menunjukkan sedikit atau tidak ada kesamaan.
            
        -   Contoh: judul film Michael Collins (1996) teridentifikasi sama (similar) dengan judul film Beyond Silence (Jenseits der Stille) (1996).
            

### Mendapatkan Rekomendasi

Fungsi `movie_recommendations` digunakan untuk menghasilkan rekomendasi:

```
def movie_recommendations(title, similarity_data=cosine_sim_df, items=data[['title', 'genre']], k=10):
    # Mengambil indeks film berdasarkan judul
    index = similarity_data.loc[:, title].to_numpy().argpartition(range(-1, -k, -1))
    
    # Mengambil judul film yang paling mirip (top-k)
    closest = similarity_data.columns[index[-1:-(k+2):-1]]
    
    # Menghapus film itu sendiri dari daftar rekomendasi
    closest = closest.drop(title, errors='ignore')
    
    # Menggabungkan dengan informasi judul dan genre, lalu mengambil top-k
    return pd.DataFrame(closest).merge(items).head(k)


```

**Contoh Rekomendasi:**

Untuk film "My Life (1993)", yang berdasarkan data memiliki genre "Drama", sistem merekomendasikan 10 film berikut:

-   Friendly Persuasion (1956)
    
-   Luther (2003)
    
-   Ikiru (1952)
    
-   Gospel According to St. Matthew, The (Vangelo secondo Matteo) (1964)
    
-   Lost Horizon (1937)
    
-   Cat on a Hot Tin Roof (1958)
    
-   Seabiscuit (2003)
    
-   Secret Lives of Dentists, The (2002)
    
-   Magdalene Sisters, The (2002)
    
-   Anastasia (1956)
    

Terlihat bahwa semua rekomendasi memiliki genre "Drama", yang konsisten dengan genre film input.

## Evaluation

Pada bagian ini, metrik evaluasi _Precision_ digunakan untuk mengukur kinerja sistem rekomendasi.

### Precision Matrix

_Precision_ adalah metrik yang mengukur proporsi item yang direkomendasikan yang sebenarnya relevan bagi pengguna. Dalam konteks sistem rekomendasi, _precision_ menjawab pertanyaan: "Dari semua item yang direkomendasikan, berapa banyak yang benar-benar relevan?"

Formula _Precision_ adalah sebagai berikut:

![Alt Text](https://assets.cdn.dicoding.com/original/academy/dos:819311f78d87da1e0fd8660171fa58e620211012160253.png) 

**Perhitungan Precision untuk Rekomendasi "My Life (1993)":**

Untuk tujuan evaluasi ini, kita membuat asumsi bahwa semua film yang direkomendasikan dengan genre "Drama" dianggap relevan, mengingat film input "My Life (1993)" juga bergenre "Drama" dan sistem ini berbasis konten genre.


* **Jumlah rekomendasi yang relevan:** Berdasarkan asumsi kita, semua 10 film bergenre "Drama" dianggap relevan. Jadi, jumlah rekomendasi yang relevan adalah 10.

* **Jumlah item yang direkomendasikan:** Ada 10 film dalam daftar rekomendasi.

  

Maka, persisinya adalah:

  

$\qquad P = \frac{10}{10} = 1$



Berdasarkan asumsi bahwa semua film bergenre "Drama" relevan, matriks persisi untuk rekomendasi film setelah menonton "My Life (1993)" adalah **1**. Ini berarti bahwa 100% film yang direkomendasikan dianggap relevan berdasarkan genre.

**Kesimpulan Evaluasi:**

Berdasarkan asumsi bahwa semua film bergenre "Drama" adalah relevan, sistem rekomendasi ini mencapai nilai _Precision_  **1**. Ini menunjukkan bahwa 100% dari film yang direkomendasikan untuk "My Life (1993)" adalah relevan berdasarkan kriteria genre.
