Link Drive (APKs and Screenshots): https://drive.google.com/drive/folders/1RVx8ix1sN54mD6eykm-bVNcFxUsWS2zq?usp=sharing

Images:
<img width="369" height="664" alt="Commit2 Part3 1" src="https://github.com/user-attachments/assets/2723d461-66fe-4ad2-a795-5e8cd16233e6" />
<img width="368" height="662" alt="Commit2 Part3 2" src="https://github.com/user-attachments/assets/847a7aae-b2aa-4467-89d0-c18937151d1f" />

Assignment:
[LAB_WEEK_11.txt](https://github.com/user-attachments/files/23696054/LAB_WEEK_11.txt)
Vanessa Audrelia Christianto (00000104990)

Based on Part 3 of the tutorial:

1. When a user takes a picture, that picture is stored in a path based on the given
URI. In which part of the code handles this? (Copy that part of the code as the
answer)
Answer:
Yang menyimpan file hasil foto ke MediaStore adalah fungsi insertToStore() di ProviderFileManager.kt.
Kode:
private fun insertToStore(fileInfo: FileInfo, contentUri: Uri,
                              contentValues: ContentValues) {
        executor.execute {
            val insertedUri = contentResolver.insert(contentUri,
                contentValues)
            insertedUri?.let {
                val inputStream =
                    contentResolver.openInputStream(fileInfo.uri)
                val outputStream =
                    contentResolver.openOutputStream(insertedUri)
                IOUtils.copy(inputStream, outputStream)
            }
        }

2. In your FileInfo.kt, there are 5 attributes. On the first attribute, what does the URI
refer to? And on the fourth attribute, what does relativePath refer to?
Answer:
Atribut 1 (URI): adalah URI sementara yang diberikan oleh FileProvider, dipakai oleh kamera untuk menuliskan hasil foto/video. URI ini sifatnya sementara, belum masuk MediaStore.
Atribut 4 (relativePath): adalah lokasi penyimpanan di dalam MediaStore, misalnya "Pictures/" atau "Movies/". Field ini dipakai saat membuat ContentValues untuk MediaStore.

3. [Bonus] Explain the chronological order from when a user takes a picture until
the file is stored in the MediaStore.
Answer:
    1. User menekan tombol "Photo"
       -> isCapturingVideo = false
       -> App memanggil checkStoragePermission { openImageCapture() }
    2. App membuat FileInfo untuk foto
       -> generatePhotoUri() dipanggil.
       -> File baru dibuat di Android/data/package/files/Pictures
       -> FileProvider membuat URI sementara untuk file tersebut
       -> Semua info dimasukkan ke photoInfo
    3. TakePictureLauncher dijalankan
       -> takePictureLauncher.launch(photoInfo!!.uri)
       -> Kamera Android dibuka
       -> Kamera menulis hasil foto ke file sesuai URI sementara tadi
    4. Setelah kamera selesai, callback dijalankan
       -> providerFileManager.insertImageToStore(photoInfo)
    5. ProviderFileManager memasukkan file ke MediaStore
       -> insertToStore() dipanggil:
          -> Membuat URI baru untuk MediaStore (folder Pictures)
          -> Meng-copy file dari URI sementara ke MediaStore
          -> Output stream menulis file ke DCIM/Pictures/ (folder default gallery)
    6. Foto sekarang muncul di Gallery
       -> Karena sudah berada di MediaStore dengan MIME type & path yang benar
