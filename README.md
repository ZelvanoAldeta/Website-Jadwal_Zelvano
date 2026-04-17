
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jadwal 11 TJKT | Whibie Aldyansyah S.</title>
    <style>
        :root {
            --bg-dark: #0f172a;
            --card-bg: #1e293b;
            --accent-blue: #38bdf8;
            --text-main: #f8fafc;
            --text-dim: #94a3b8;
            --success: #22c55e;
            --border: #334155;
        }

        body {
            font-family: 'Inter', -apple-system, sans-serif;
            background-color: var(--bg-dark);
            color: var(--text-main);
            margin: 0;
            padding: 40px 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        .container {
            width: 100%;
            max-width: 1100px;
            background: var(--card-bg);
            border-radius: 20px;
            border: 1px solid var(--border);
            box-shadow: 0 25px 50px -12px rgba(0,0,0,0.5);
            padding: 30px;
        }

        .header {
            text-align: center;
            margin-bottom: 40px;
            border-bottom: 1px solid var(--border);
            padding-bottom: 20px;
        }

        .header h1 {
            margin: 0;
            font-size: 2.5rem;
            background: linear-gradient(to right, #38bdf8, #818cf8);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .header p {
            color: var(--text-dim);
            font-size: 1.1rem;
            letter-spacing: 2px;
            text-transform: uppercase;
            margin-top: 10px;
        }

        .toolbar {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }

        .badge {
            background: rgba(56, 189, 248, 0.1);
            color: var(--accent-blue);
            padding: 5px 15px;
            border-radius: 20px;
            font-size: 0.8rem;
            border: 1px solid var(--accent-blue);
        }

        /* Table Styling */
        .table-wrapper {
            overflow-x: auto;
            border-radius: 12px;
            border: 1px solid var(--border);
        }

        table {
            width: 100%;
            border-collapse: collapse;
            background: #1e293b;
        }

        th {
            background: #0f172a;
            color: var(--accent-blue);
            padding: 20px;
            text-transform: uppercase;
            font-size: 0.9rem;
            letter-spacing: 1px;
            text-align: center;
        }

        td {
            padding: 15px;
            border: 1px solid var(--border);
            text-align: center;
            transition: all 0.3s;
            min-width: 120px;
        }

        td:first-child {
            background: #0f172a;
            font-weight: bold;
            color: var(--text-dim);
        }

        /* Fitur Edit */
        td[contenteditable="true"]:focus {
            outline: 2px solid var(--accent-blue);
            background: rgba(56, 189, 248, 0.05);
        }

        .mapel-jurusan { color: #fbbf24; font-weight: 600; }
        .mapel-umum { color: #f8fafc; }

        .btn-save {
            background: var(--accent-blue);
            color: #0f172a;
            border: none;
            padding: 12px 25px;
            border-radius: 8px;
            font-weight: bold;
            cursor: pointer;
            transition: transform 0.2s, background 0.2s;
        }

        .btn-save:hover {
            transform: translateY(-2px);
            background: #7dd3fc;
        }

        .footer {
            margin-top: 20px;
            color: var(--text-dim);
            font-size: 0.85rem;
            display: flex;
            gap: 20px;
        }

        .legend { display: flex; gap: 15px; align-items: center; }
        .dot { width: 10px; height: 10px; border-radius: 50%; display: inline-block; }
    </style>
</head>
<body>

<div class="container">
    <div class="header">
        <h1>Whibie Aldyansyah S.</h1>
        <p>Kelas 11 TJKT - Schedule System</p>
    </div>

    <div class="toolbar">
        <div class="badge">Mode: Interactive Editor</div>
        <button class="btn-save" onclick="saveData()">SIMPAN JADWAL</button>
    </div>

    <div class="table-wrapper">
        <table id="jadwalTable">
            <thead>
                <tr>
                    <th>Waktu</th>
                    <th>Senin</th>
                    <th>Selasa</th>
                    <th>Rabu</th>
                    <th>Kamis</th>
                    <th>Jumat</th>
                </tr>
            </thead>
            <tbody id="tableBody">
                </tbody>
        </table>
    </div>

    <div class="footer">
        <div class="legend">
            <span class="dot" style="background:#fbbf24"></span> Pelajaran Jurusan
        </div>
        <div class="legend">
            <span class="dot" style="background:#f8fafc"></span> Pelajaran Umum
        </div>
        <div>* Klik pada teks pelajaran untuk mengubah isi</div>
    </div>
</div>

<script>
    // Data Default (Pelajaran Umum & Jurusan)
    const defaultData = [
        ["07:30 - 09:00", "AIJ", "Matematika", "TLJ", "B. Inggris", "PKK"],
        ["09:15 - 10:45", "ASJ", "B. Indonesia", "Wan", "Pkn", "Agama"],
        ["11:00 - 12:30", "Keamanan Jaringan", "Fisika", "Sistem Radio", "Sejarah", "Olahraga"]
    ];

    const jurusanKeywords = ["AIJ", "ASJ", "TLJ", "Wan", "Keamanan", "Jaringan", "Radio", "Cloud", "TJKT", "PKK"];

    function initTable() {
        const savedData = JSON.parse(localStorage.getItem('whibieJadwal')) || defaultData;
        const tbody = document.getElementById('tableBody');
        tbody.innerHTML = '';

        savedData.forEach((rowData, rowIndex) => {
            const tr = document.createElement('tr');
            rowData.forEach((cellData, cellIndex) => {
                const td = document.createElement('td');
                
                // Kolom jam tidak bisa diedit
                if (cellIndex !== 0) {
                    td.contentEditable = "true";
                    // Cek apakah pelajaran jurusan
                    const isJurusan = jurusanKeywords.some(key => cellData.toLowerCase().includes(key.toLowerCase()));
                    td.className = isJurusan ? "mapel-jurusan" : "mapel-umum";
                }

                td.innerText = cellData;
                tr.appendChild(td);
            });
            tbody.appendChild(tr);
        });
    }

    function saveData() {
        const rows = document.querySelectorAll('#tableBody tr');
        const newData = [];

        rows.forEach(row => {
            const rowData = [];
            row.querySelectorAll('td').forEach(cell => {
                rowData.push(cell.innerText);
            });
            newData.push(rowData);
        });

        localStorage.setItem('whibieJadwal', JSON.stringify(newData));
        alert('Jadwal Whibie Aldyansyah S. berhasil disimpan!');
        initTable(); // Refresh warna
    }

    // Jalankan inisialisasi
    initTable();
</script>

</body>
</html># Website-Jadwal_Zelvano
Web Whibie Aldyansyah S.
