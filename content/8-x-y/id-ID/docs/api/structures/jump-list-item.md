# Objek JumpListItem

* `type` String (opsional) - Salah satu dari:
  * ` tugas </ 0> - Tugas akan meluncurkan sebuah aplikasi dengan argumen tertentu.</li>
<li><code> separator </ 0> - Dapat digunakan untuk memisahkan item dalam kategori <code> Tugas </ 0> 
kategori.</li>
<li><code> file </ 0> - Sebuah file link akan membuka file menggunakan aplikasi yang membuat Jump List, agar aplikasi ini harus didaftarkan sebagai penangan untuk jenis file (walaupun tidak harus penangan default).</li>
</ul></li>
<li><p spaces-before="0"><code> path </ 0>  String (opsional) - Path dari file yang akan dibuka, hanya boleh diset jika <code> type </ 0> adalah
 <code> file </ 0> .</p></li>
<li><p spaces-before="0"><code>program` String (optional) - Path of the program to execute, usually you should specify `process.execPath` which opens the current program. Sebaiknya disetel jika ` ketik </ 0> adalah <code> tugas </ 0> .</p></li>
<li><p spaces-before="0"><code>args` String (opsional) - argumen dari baris perintah saat `program` dijalankan. Sebaiknya hanya disetel jika `type` adalah `task`.</p>
* `title` String (opsional) - Teks yang akan ditampilkan dalam Daftar Lompatan. Sebaiknya hanya disetel jika `type` adalah `task`.
* `description` String (opsional) - Deskripsi tugas (ditampilkan di keterangan). Seharusnya hanya disetel jika `type` adalah `task`.
* `iconPath` String (optional) - The absolute path to an icon to be displayed in a Jump List, which can be an arbitrary resource file that contains an icon (e.g. `.ico`, `.exe`, `.dll`). Anda biasanya dapat menentukan ` process.execPath </ 0> untuk menampilkan ikon program.</p></li>
<li><p spaces-before="0"><code>iconIndex` Number (optional) - The index of the icon in the resource file. Jika file sumber daya berisi beberapa ikon, nilai ini dapat digunakan untuk menentukan indeks berbasis nol dari ikon yang harus ditampilkan untuk tugas ini. Jika file sumber hanya berisi satu ikon, properti ini harus diset ke nol.
* `workingDirectory` String (opsional) - Direktoiri kerja. Kosong secara default.
