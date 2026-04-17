<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jadwal Belajar | Whibie Aldyansyah S.</title>
    <style>
        :root {
            --primary-blue: #0ea5e9;
            --light-blue: #e0f2fe;
            --bg-body: #f8fafc;
            --text-dark: #0f172a;
            --text-light: #64748b;
            --white: #ffffff;
            --success: #10b981;
            --danger: #f43f5e;
            --border: #e2e8f0;
        }

        body {
            font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--bg-body);
            color: var(--text-dark);
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
        }

        .main-card {
            width: 100%;
            max-width: 1000px;
            background: var(--white);
            border-radius: 16px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.05);
            padding: 30px;
            border: 1px solid var(--border);
        }

        .header {
            text-align: center;
            margin-bottom: 30px;
        }

        .header h1 {
            margin: 0;
            color: var(--primary-blue);
            font-size: 1.8rem;
        }

        .header p {
            margin: 5px 0;
            color: var(--text-light);
            font-weight: 500;
            letter-spacing: 1px;
        }

        /* Control Panel */
        .controls {
            display: flex;
            background: var(--light-blue);
            padding: 15px;
            border-radius: 12px;
            gap: 10px;
            margin-bottom: 25px;
            align-items: center;
            flex-wrap: wrap;
        }

        input[type="text"] {
            padding: 10px 15px;
            border: 1px solid var(--border);
            border-radius: 8px;
            flex: 1;
            min-width: 200px;
            outline: none;
        }

        .btn {
            padding: 10px 20px;
            border: none;
            border-radius: 8px;
            font-weight: 600;
            cursor: pointer;
            transition: 0.2s;
            display: inline-flex;
            align-items: center;
            gap: 5px;
        }

        .btn-add { background: var(--primary-blue); color: white; }
        .btn-save { background: var(--success); color: white; }
        .btn-reset { background: transparent; color: var(--danger); border: 1px solid var(--danger); }
        .btn:hover { opacity: 0.9; transform: translateY(-1px); }

        /* Table Styling */
        .table-area {
            overflow-x: auto;
            border-radius: 10px;
            border: 1px solid var(--border);
        }

        table {
            width: 100%;
            border-collapse: collapse;
            background: var(--white);
        }

        th {
            background: var(--primary-blue);
            color: var(--white);
            padding: 15px;
            font-size: 0.9rem;
            text-transform: uppercase;
        }

        td {
            padding: 12px;
            border: 1px solid var(--border);
            text-align: center;
            font-size: 0.95rem;
            min-width: 120px;
        }

        /* Cell Edit Mode */
        td[contenteditable="true"]:focus {
            background: #fff;
            outline: 2px solid var(--primary-blue);
            box-shadow: inset 0 0 5px rgba(0,0,0,0.1);
        }

        .time-cell {
            background: var(--bg-body);
            font-weight: bold;
            color: var(--primary-blue);
        }

        .action-cell { width: 50px; color: var(--danger); cursor: pointer; font-weight: bold; }

        .hint {
            margin-top: 15px;
            font-size: 0.85rem;
            color: var(--text-light);
            font-style: italic;
        }
    </style>
</head>
<body>

<div class="main-card">
    <div class="header">
        <p>JADWAL BELAJAR 11 TJKT</p>
        <h1>Whibie Aldyansyah S.</h1>
    </div>

    <div class="controls">
        <input type="text" id="timeInput" placeholder="Masukkan Waktu (Contoh: 07:30 - 09:00)">
        <button class="btn btn-add" onclick="autoAddRow()">+ Tambah Baris</button>
        <button class="btn btn-save" onclick="saveJadwal()">💾 Simpan Jadwal</button>
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
                    <th style="background: #f1f5f9; color: #64748b;">Aksi</th>
                </tr>
            </thead>
            <tbody id="scheduleBody">
                </tbody>
        </table>
    </div>

    <div class="hint">
        * Tip: Klik pada kotak mana saja untuk mengubah isi pelajaran. Klik "Simpan Jadwal" agar data tidak hilang.
    </div>
</div>

<script>
    // Load data saat pertama kali buka
    window.onload = function() {
        const savedData = JSON.parse(localStorage.getItem('jadwal_whibie_v3'));
        if (savedData && savedData.length > 0) {
            renderTable(savedData);
        } else {
            // Data awal contoh
            const defaultRows = [
                ["07:30 - 09:00", "Matematika", "AIJ", "B. Inggris", "ASJ", "Agama"]
            ];
            renderTable(defaultRows);
        }
    };

    // Fungsi Tambah Baris Otomatis
    function autoAddRow() {
        const timeVal = document.getElementById('timeInput').value;
        if (!timeVal) {
            alert("Silakan masukkan rentang waktu terlebih dahulu!");
            return;
        }

        const tbody = document.getElementById('scheduleBody');
        const tr = document.createElement('tr');
        
        // Buat 6 kolom otomatis (Waktu + 5 Hari)
        let rowHtml = `<td class="time-cell" contenteditable="true">${timeVal}</td>`;
        for (let i = 0; i < 5; i++) {
            rowHtml += `<td contenteditable="true">Isi Pelajaran</td>`;
        }
        rowHtml += `<td class="action-cell" onclick="deleteRow(this)">×</td>`;
        
        tr.innerHTML = rowHtml;
        tbody.appendChild(tr);
        document.getElementById('timeInput').value = ''; // Reset input
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
        if(confirm("Hapus baris ini?")) {
            btn.parentElement.remove();
        }
    }

    function saveJadwal() {
        const rows = document.querySelectorAll('#scheduleBody tr');
        const allData = [];

        rows.forEach(row => {
            const rowData = [];
            row.querySelectorAll('td:not(.action-cell)').forEach(cell => {
                rowData.push(cell.innerText);
            });
            allData.push(rowData);
        });

        localStorage.setItem('jadwal_whibie_v3', JSON.stringify(allData));
        alert("Jadwal Whibie Aldyansyah S. berhasil disimpan di browser!");
    }

    function resetJadwal() {
        if(confirm("Hapus semua jadwal dan reset?")) {
            localStorage.removeItem('jadwal_whibie_v3');
            location.reload();
        }
    }
</script>

</body>
</html>
