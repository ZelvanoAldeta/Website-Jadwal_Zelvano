<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jadwal Belajar | Whibie Aldyansyah S.</title>
    <style>
        :root {
            --primary: #0284c7; /* Biru cerah */
            --secondary: #e0f2fe; /* Biru muda */
            --bg: #f8fafc;
            --white: #ffffff;
            --text-main: #0f172a;
            --text-sub: #64748b;
            --success: #10b981;
            --danger: #f43f5e;
            --border: #e2e8f0;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg);
            color: var(--text-main);
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
        }

        .container {
            width: 100%;
            max-width: 1100px;
            background: var(--white);
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.05);
            padding: 30px;
            border: 1px solid var(--border);
        }

        /* Header Section */
        .header {
            text-align: center;
            margin-bottom: 30px;
            padding-bottom: 20px;
            border-bottom: 2px solid var(--secondary);
        }

        .header h1 {
            margin: 0;
            color: var(--primary);
            font-size: 2rem;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .header p {
            margin: 5px 0 0;
            color: var(--text-sub);
            font-size: 1.1rem;
            font-weight: 600;
        }

        /* Control Panel */
        .controls {
            display: flex;
            background: var(--secondary);
            padding: 20px;
            border-radius: 15px;
            gap: 12px;
            margin-bottom: 25px;
            align-items: center;
            flex-wrap: wrap;
        }

        .controls input {
            padding: 12px 15px;
            border: 1px solid var(--border);
            border-radius: 8px;
            flex: 1;
            min-width: 250px;
            font-size: 1rem;
            outline: none;
        }

        .controls input:focus {
            border-color: var(--primary);
            box-shadow: 0 0 0 3px rgba(2, 132, 199, 0.1);
        }

        .btn {
            padding: 12px 24px;
            border: none;
            border-radius: 8px;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.2s;
            display: flex;
            align-items: center;
            gap: 8px;
            font-size: 0.95rem;
        }

        .btn-add { background: var(--primary); color: white; }
        .btn-save { background: var(--success); color: white; }
        .btn-reset { background: white; color: var(--danger); border: 1px solid var(--danger); }
        .btn:hover { opacity: 0.9; transform: translateY(-1px); }
        .btn:active { transform: translateY(0); }

        /* Table Styling */
        .table-area {
            overflow-x: auto;
            border-radius: 12px;
            border: 1px solid var(--border);
        }

        table {
            width: 100%;
            border-collapse: collapse;
            background: var(--white);
        }

        th {
            background: var(--primary);
            color: var(--white);
            padding: 18px;
            font-size: 0.9rem;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        td {
            padding: 15px;
            border: 1px solid var(--border);
            text-align: center;
            font-size: 1rem;
            min-width: 140px;
        }

        /* Spesial Kolom Waktu */
        .time-cell {
            background: #f1f5f9;
            font-weight: bold;
            color: var(--primary);
            width: 150px;
        }

        /* Cell Edit Mode */
        td[contenteditable="true"]:focus {
            background: #fff;
            outline: 2px solid var(--primary);
            box-shadow: inset 0 0 8px rgba(0,0,0,0.05);
        }

        .action-cell {
            width: 60px;
            color: var(--danger);
            cursor: pointer;
            font-weight: bold;
            font-size: 1.2rem;
            background: #fff1f2;
        }

        .action-cell:hover { background: #ffe4e6; }

        .footer-info {
            margin-top: 20px;
            font-size: 0.85rem;
            color: var(--text-sub);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        /* Animasi masuk */
        tr { animation: fadeIn 0.3s ease-in; }
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
    </style>
</head>
<body>

<div class="container">
    <div class="header">
        <h1>Whibie Aldyansyah S.</h1>
        <p>Jadwal Belajar Kelas 11 TJKT</p>
    </div>

    <div class="controls">
        <input type="text" id="timeInput" placeholder="Ketik Waktu (Misal: 07:30 - 09:00)">
        <button class="btn btn-add" onclick="autoAddRow()">+ Tambah Baris</button>
        <button class="btn btn-save" onclick="saveData()">💾 Simpan Jadwal</button>
        <button class="btn btn-reset" onclick="resetJadwal()">Reset</button>
    </div>

    <div class="table-area">
        <table>
            <thead>
                <tr>
                    <th>Waktu</th>
                    <th>Senin</th>
                    <th>Selasa</th>
                    <th>Rabu</th>
                    <th>Kamis</th>
                    <th>Jumat</th>
                    <th style="background: #cbd5e1; color: #475569;">Hapus</th>
                </tr>
            </thead>
            <tbody id="scheduleBody">
                </tbody>
        </table>
    </div>

    <div class="footer-info">
        <span>* Klik langsung pada teks untuk mengedit isi pelajaran.</span>
        <span>Versi: 1.0 (Modern Light)</span>
    </div>
</div>

<script>
    // Memuat data dari memori browser saat halaman dibuka
    window.onload = function() {
        const saved = localStorage.getItem('jadwal_whibie_final');
        if (saved) {
            renderTable(JSON.parse(saved));
        } else {
            // Data default saat pertama kali dijalankan
            const defaultData = [
                ["07:30 - 09:00", "Matematika", "AIJ", "B. Indonesia", "TLJ", "Agama"],
                ["09:15 - 10:45", "B. Inggris", "ASJ", "Pkn", "WAN", "PJOK"]
            ];
            renderTable(defaultData);
        }
    };

    // Fungsi Tambah Baris Otomatis
    function autoAddRow() {
        const timeVal = document.getElementById('timeInput').value;
        if (!timeVal) {
            alert("Harap isi rentang waktu terlebih dahulu!");
            return;
        }

        const tbody = document.getElementById('scheduleBody');
        const tr = document.createElement('tr');
        
        // Buat 6 kolom otomatis: 1 Jam + 5 Hari
        let rowHtml = `<td class="time-cell" contenteditable="true">${timeVal}</td>`;
        for (let i = 0; i < 5; i++) {
            rowHtml += `<td contenteditable="true">Pelajaran</td>`;
        }
        rowHtml += `<td class="action-cell" onclick="deleteRow(this)">×</td>`;
        
        tr.innerHTML = rowHtml;
        tbody.appendChild(tr);
        document.getElementById('timeInput').value = ''; // Kosongkan input
    }

    function renderTable(dataArray) {
        const tbody = document.getElementById('scheduleBody');
        tbody.innerHTML = '';
        dataArray.forEach(rowData => {
            const tr = document.createElement('tr');
            let rowHtml = `<td class="time-cell" contenteditable="true">${rowData[0]}</td>`;
            for (let i = 1; i < 6; i++) {
                rowHtml += `<td contenteditable="true">${rowData[i] || '-'}</td>`;
            }
            rowHtml += `<td class="action-cell" onclick="deleteRow(this)">×</td>`;
            tr.innerHTML = rowHtml;
            tbody.appendChild(tr);
        });
    }

    function deleteRow(btn) {
        if(confirm("Hapus baris jadwal ini?")) {
            btn.parentElement.remove();
        }
    }

    function saveData() {
        const rows = document.querySelectorAll('#scheduleBody tr');
        const allData = [];

        rows.forEach(row => {
            const rowData = [];
            row.querySelectorAll('td:not(.action-cell)').forEach(cell => {
                rowData.push(cell.innerText);
            });
            allData.push(rowData);
        });

        localStorage.setItem('jadwal_whibie_final', JSON.stringify(allData));
        alert("Jadwal Berhasil Disimpan! Data kamu aman meskipun browser ditutup.");
    }

    function resetJadwal() {
        if(confirm("Apakah kamu yakin ingin menghapus semua data dan reset ke awal?")) {
            localStorage.removeItem('jadwal_whibie_final');
            location.reload();
        }
    }
</script>

</body>
</html>
