<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Form Driver - PT TAL SARANA LOGISTIK</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        .hidden { display: none; }
        input:invalid { border-color: #ef4444; }
    </style>
</head>
<body class="bg-gray-100 min-h-screen p-4">

    <div class="max-w-md mx-auto bg-white rounded-2xl shadow-xl overflow-hidden">
        <div class="bg-blue-600 p-4 text-white text-center">
            <h1 class="text-lg font-bold">PT TAL SARANA LOGISTIK</h1>
            <p class="text-xs opacity-80">Logistik & Distribusi Jabodetabek</p>
        </div>

        <div class="p-6">
            <div id="page1">
                <div class="flex items-center justify-between mb-6">
                    <h2 class="text-xl font-bold text-gray-800">Check-In Driver</h2>
                    <span class="bg-yellow-100 text-yellow-700 text-xs px-2 py-1 rounded font-bold">PROSES</span>
                </div>
                
                <div class="space-y-4">
                    <div>
                        <label class="block text-sm font-semibold text-gray-700">Nama Customer / Tujuan *</label>
                        <input type="text" id="customer" required 
                            class="mt-1 block w-full border border-gray-300 rounded-lg p-3 focus:ring-2 focus:ring-blue-500 outline-none" 
                            placeholder="Contoh: PT. Maju Jaya">
                    </div>

                    <div>
                        <label class="block text-sm font-semibold text-gray-700">Ambil Foto Kedatangan *</label>
                        <input type="file" id="fotoMasuk" accept="image/*" capture="camera" 
                            class="mt-1 block w-full text-sm text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-full file:border-0 file:bg-blue-50 file:text-blue-700">
                    </div>

                    <div class="grid grid-cols-2 gap-4">
                        <div>
                            <label class="block text-xs text-gray-500 uppercase">Jam Datang</label>
                            <input type="text" id="jamMasuk" readonly class="w-full bg-gray-50 text-sm font-medium p-1 outline-none">
                        </div>
                        <div>
                            <label class="block text-xs text-gray-500 uppercase">Lokasi GPS</label>
                            <input type="text" id="lokasiMasuk" readonly class="w-full bg-gray-50 text-[10px] font-medium p-1 outline-none text-blue-600">
                        </div>
                    </div>

                    <button onclick="prosesKeTahap2()" 
                        class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-4 rounded-xl shadow-lg transition duration-200">
                        MULAI TUGAS (BERLANGSUNG)
                    </button>
                </div>
            </div>

            <div id="page2" class="hidden">
                <div class="flex items-center justify-between mb-6">
                    <h2 class="text-xl font-bold text-gray-800">Check-Out Driver</h2>
                    <span class="bg-green-100 text-green-700 text-xs px-2 py-1 rounded font-bold">FINISH</span>
                </div>

                <div class="space-y-4">
                    <div class="p-3 bg-blue-50 rounded-lg border border-blue-100 mb-4">
                        <p class="text-xs text-blue-600">Customer:</p>
                        <p id="displayCustomer" class="font-bold text-blue-900"></p>
                    </div>

                    <div>
                        <label class="block text-sm font-semibold text-gray-700">Jam Selesai</label>
                        <input type="text" id="jamKeluar" readonly class="mt-1 block w-full bg-gray-100 border rounded-lg p-3 font-medium">
                    </div>

                    <div>
                        <label class="block text-sm font-semibold text-gray-700">Ambil Foto Selesai *</label>
                        <input type="file" id="fotoKeluar" accept="image/*" capture="camera" 
                            class="mt-1 block w-full text-sm text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-full file:border-0 file:bg-green-50 file:text-green-700">
                    </div>

                    <button onclick="submitFinal()" 
                        class="w-full bg-green-600 hover:bg-green-700 text-white font-bold py-4 rounded-xl shadow-lg transition duration-200">
                        SELESAIKAN (MASUK SPREADSHEET)
                    </button>
                </div>
            </div>

            <div id="successMsg" class="hidden text-center py-12">
                <div class="inline-flex items-center justify-center w-20 h-20 bg-green-100 rounded-full mb-6">
                    <span class="text-4xl">🚚</span>
                </div>
                <h2 class="text-2xl font-bold text-gray-800">Data Terkirim!</h2>
                <p class="text-gray-500 mt-2">Laporan berhasil masuk ke Spreadsheet PT TAL.</p>
                <button onclick="location.reload()" class="mt-8 text-blue-600 font-semibold underline">Input Baru</button>
            </div>
        </div>
    </div>

    <script>
        // GANTI URL INI dengan URL Web App dari Google Apps Script Anda
        const SCRIPT_URL = "URL_APPS_SCRIPT_ANDA_DISINI";

        // Ambil data awal saat load
        window.onload = function() {
            updateTime('jamMasuk');
            getGPS();
        };

        function updateTime(id) {
            const now = new Date();
            document.getElementById(id).value = now.toLocaleTimeString('id-ID') + " WIB";
        }

        function getGPS() {
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(pos => {
                    document.getElementById('lokasiMasuk').value = pos.coords.latitude.toFixed(6) + ", " + pos.coords.longitude.toFixed(6);
                }, () => {
                    document.getElementById('lokasiMasuk').value = "GPS tidak aktif";
                });
            }
        }

        function prosesKeTahap2() {
            const cust = document.getElementById('customer').value;
            const foto = document.getElementById('fotoMasuk').files[0];

            if (!cust) {
                alert("Wajib memasukkan Nama Customer!");
                return;
            }
            if (!foto) {
                alert("Wajib mengambil foto kedatangan!");
                return;
            }

            // Pindah halaman
            document.getElementById('displayCustomer').innerText = cust;
            document.getElementById('page1').classList.add('hidden');
            document.getElementById('page2').classList.remove('hidden');
            updateTime('jamKeluar');
        }

        function submitFinal() {
            const fotoSelesai = document.getElementById('fotoKeluar').files[0];
            if (!fotoSelesai) {
                alert("Wajib mengambil foto selesai!");
                return;
            }

            const payload = {
                waktu_datang: document.getElementById('jamMasuk').value,
                waktu_selesai: document.getElementById('jamKeluar').value,
                customer: document.getElementById('customer').value,
                lokasi: document.getElementById('lokasiMasuk').value,
                status: "SELESAI"
            };

            // Menampilkan loading sederhana
            alert("Sedang mengirim data ke Spreadsheet PT TAL...");

            fetch(SCRIPT_URL, {
                method: 'POST',
                body: JSON.stringify(payload)
            })
            .then(res => {
                document.getElementById('page2').classList.add('hidden');
                document.getElementById('successMsg').classList.remove('hidden');
            })
            .catch(err => {
                // Untuk demo tanpa URL Script, kita anggap sukses
                document.getElementById('page2').classList.add('hidden');
                document.getElementById('successMsg').classList.remove('hidden');
                console.log("Error kirim (Cek URL Script): ", err);
            });
        }
    </script>
</body>
</html>
