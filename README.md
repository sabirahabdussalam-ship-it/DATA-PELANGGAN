# DATA-PELANGGAN

<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PD-PELANGGAN | Database Terpadu Cloud</title>
    <!-- Bootstrap & Google Fonts -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        body { background-color: #f0f2f5; font-family: 'Inter', sans-serif; color: #333; }
        .navbar { background-color: #003366; border-bottom: 3px solid #ffc107; }
        .card { border-radius: 15px; border: none; box-shadow: 0 4px 15px rgba(0,0,0,0.05); }
        .btn-primary { background-color: #003366; border: none; transition: 0.3s; }
        .btn-primary:hover { background-color: #002244; transform: translateY(-2px); }
        .table thead { background-color: #f8f9fa; text-transform: uppercase; font-size: 0.75rem; letter-spacing: 1px; }
        .form-label { font-size: 0.85rem; color: #555; }
    </style>
</head>
<body>

<nav class="navbar navbar-dark p-3 shadow-sm">
    <div class="container">
        <a class="navbar-brand fw-bold" href="#">📊 PANGKALAN DATA TERPADU</a>
    </div>
</nav>

<div class="container my-5">
    <div class="row g-4">
        <!-- FORM INPUT (KIRI) -->
        <div class="col-lg-4">
            <div class="card p-4">
                <h5 class="fw-bold text-primary mb-4 text-center">Registrasi Data Baru</h5>
                <form id="formPelanggan">
                    <div class="mb-2">
                        <label class="form-label fw-bold">Nama Lengkap</label>
                        <input type="text" id="nama" class="form-control" placeholder="Contoh: Budi Santoso" required>
                    </div>
                    <div class="mb-2">
                        <label class="form-label fw-bold">Nomor WhatsApp/Telepon</label>
                        <input type="text" id="telp" class="form-control" placeholder="0812xxxx">
                    </div>
                    <div class="mb-2">
                        <label class="form-label fw-bold">NPWP</label>
                        <input type="text" id="npwp" class="form-control" placeholder="00.000.000.0-000.000">
                    </div>
                    <div class="mb-2">
                        <label class="form-label fw-bold">Nomor Rekening</label>
                        <input type="text" id="rekening" class="form-control" placeholder="BCA - 1234567890" required>
                    </div>
                    <div class="mb-4">
                        <label class="form-label fw-bold text-danger">Alamat Lengkap</label>
                        <textarea id="alamat" class="form-control" rows="3" placeholder="Masukkan alamat lengkap rumah/kantor..." required></textarea>
                    </div>
                    <button type="submit" class="btn btn-primary w-100 fw-bold py-2 shadow">SIMPAN DATA KE CLOUD</button>
                </form>
            </div>
        </div>

        <!-- TABEL DATA (KANAN) -->
        <div class="col-lg-8">
            <div class="card overflow-hidden">
                <div class="card-header bg-white py-3 border-bottom d-flex justify-content-between align-items-center">
                    <h6 class="mb-0 fw-bold text-secondary">Pangkalan Data Pelanggan Terdaftar</h6>
                </div>
                <div class="table-responsive">
                    <table class="table table-hover align-middle mb-0">
                        <thead>
                            <tr>
                                <th class="ps-4">Biodata</th>
                                <th>NPWP & Rekening</th>
                                <th>Alamat</th>
                                <th class="text-center">Aksi</th>
                            </tr>
                        </thead>
                        <tbody id="tabelBody">
                            <!-- Data otomatis ditarik dari Firebase -->
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</div>

<footer class="text-center py-4 text-muted small">
    &copy; 2026 Pangkalan Data Cloud Terintegrasi. Powered by GitHub & Firebase.
</footer>

<!-- SDK FIREBASE (Penghubung Data) -->
<script type="module">
  import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
  import { getDatabase, ref, push, onValue, remove } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

  // --- GANTI DENGAN KODE FIREBASE CONFIG ASLI MILIKMU ---
  const firebaseConfig = {
    apiKey: "AIzaSy...", 
    authDomain: "proyek-kamu.firebaseapp.com",
    databaseURL: "https://proyek-kamu-default-rtdb.firebaseio.com",
    projectId: "proyek-kamu",
    storageBucket: "proyek-kamu.appspot.com",
    messagingSenderId: "...",
    appId: "..."
  };

  const app = initializeApp(firebaseConfig);
  const db = getDatabase(app);
  const dbRef = ref(db, 'pangkalan_data_final');

  // PROSES SIMPAN DATA
  document.getElementById('formPelanggan').addEventListener('submit', (e) => {
    e.preventDefault();
    
    push(dbRef, {
      nama: document.getElementById('nama').value,
      telp: document.getElementById('telp').value,
      npwp: document.getElementById('npwp').value,
      rekening: document.getElementById('rekening').value,
      alamat: document.getElementById('alamat').value
    });

    alert("Sukses! Data telah tersimpan di pangkalan data.");
    document.getElementById('formPelanggan').reset();
  });

  // PROSES TAMPIL DATA SECARA REAL-TIME
  onValue(dbRef, (snapshot) => {
    const tabel = document.getElementById('tabelBody');
    tabel.innerHTML = "";
    snapshot.forEach((childSnapshot) => {
      const data = childSnapshot.val();
      const key = childSnapshot.key;
      
      tabel.innerHTML += `
        <tr>
          <td class="ps-4"><strong>${data.nama}</strong><br><small class="text-muted">${data.telp || '-'}</small></td>
          <td>
            <small>NPWP: ${data.npwp || '-'}</small><br>
            <span class="text-primary small fw-bold">${data.rekening || '-'}</span>
          </td>
          <td><small class="text-dark">${data.alamat}</small></td>
          <td class="text-center">
            <button class="btn btn-sm btn-outline-danger" onclick="hapusData('${key}')">Hapus</button>
          </td>
        </tr>`;
    });
  });

  // FUNGSI HAPUS DATA
  window.hapusData = (key) => {
    if(confirm('Hapus data pelanggan ini secara permanen?')) {
        remove(ref(db, 'pangkalan_data_final/' + key));
    }
  }
</script>
</body>
</html>
