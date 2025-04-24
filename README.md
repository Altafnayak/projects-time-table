# projects-time-table
#Altaf
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Weekly Timetable</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      margin: 0;
      padding: 20px;
      background-image: url('https://wallpapercave.com/wp/wp3738652.jpg');
      background-size: cover;
      background-attachment: fixed;
      background-position: center;
      color: #fff;
      text-shadow: 1px 1px 2px #000;
    }

    .container {
      background-color: rgba(0, 0, 0, 0.7);
      padding: 30px;
      border-radius: 12px;
      max-width: 1200px;
      margin: auto;
      box-shadow: 0 0 25px rgba(0, 0, 0, 0.9);
    }

    h1 {
      text-align: center;
      margin-bottom: 30px;
      font-size: 2rem;
    }

    .day-section {
      margin-bottom: 20px;
      border-bottom: 2px solid #ccc;
      padding-bottom: 15px;
    }

    .day-section h2 {
      margin-bottom: 10px;
    }

    .slot input {
      padding: 10px;
      border-radius: 6px;
      border: none;
      margin: 5px;
      width: 200px;
    }

    button {
      display: block;
      margin: 30px auto;
      padding: 12px 25px;
      font-size: 1rem;
      background-color: #1f8cff;
      color: white;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      box-shadow: 0 0 10px #1f8cff;
    }

    table {
      width: 100%;
      margin-top: 30px;
      border-collapse: collapse;
      background-color: rgba(255, 255, 255, 0.9);
      color: #000;
    }

    th, td {
      border: 2px solid #444;
      padding: 12px;
      text-align: center;
    }

    th {
      background-color: #1f8cff;
      color: #fff;
    }

    tr:nth-child(even) {
      background-color: #f2f2f2;
    }

    #downloadBtn {
      background-color: #00cc88;
      box-shadow: 0 0 10px #00cc88;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>Weekly Class Timetable</h1>
    <form id="weekForm">
      <div id="daysContainer"></div>
      <button type="submit">Generate Timetable</button>
    </form>

    <table id="resultTable" style="display:none;">
      <thead id="tableHead"></thead>
      <tbody id="tableBody"></tbody>
    </table>

    <button id="downloadBtn" style="display:none;">Download as PDF</button>
  </div>

  <script>
    const days = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];
    const daysContainer = document.getElementById('daysContainer');

    // Create input fields for each day
    days.forEach(day => {
      const dayDiv = document.createElement('div');
      dayDiv.className = 'day-section';
      dayDiv.innerHTML = `<h2>${day}</h2>`;
      for (let i = 1; i <= 6; i++) {
        const slotDiv = document.createElement('div');
        slotDiv.className = 'slot';
        slotDiv.innerHTML = `
          <input type="text" name="${day}_subject${i}" placeholder="Subject ${i}" required>
          <input type="text" name="${day}_time${i}" placeholder="Time ${i}" required>
        `;
        dayDiv.appendChild(slotDiv);
      }
      daysContainer.appendChild(dayDiv);
    });

    const form = document.getElementById('weekForm');
    const table = document.getElementById('resultTable');
    const tableHead = document.getElementById('tableHead');
    const tableBody = document.getElementById('tableBody');
    const downloadBtn = document.getElementById('downloadBtn');

    form.addEventListener('submit', function(e) {
      e.preventDefault();
      tableHead.innerHTML = '';
      tableBody.innerHTML = '';

      // Table header
      const headRow = document.createElement('tr');
      headRow.innerHTML = `<th>Day</th>`;
      for (let i = 1; i <= 6; i++) {
        headRow.innerHTML += `<th>Subject ${i}</th>`;
      }
      tableHead.appendChild(headRow);

      // Table data
      days.forEach(day => {
        const row = document.createElement('tr');
        row.innerHTML = `<td>${day}</td>`;
        for (let i = 1; i <= 6; i++) {
          const subject = form[`${day}_subject${i}`].value || ".";
          const time = form[`${day}_time${i}`].value || ".";
          row.innerHTML += `<td>${subject}<br><small>(${time})</small></td>`;
        }
        tableBody.appendChild(row);
      });

      table.style.display = 'table';
      downloadBtn.style.display = 'block';
      table.scrollIntoView({ behavior: 'smooth' });
    });

    // PDF Download
    downloadBtn.addEventListener('click', () => {
      const { jsPDF } = window.jspdf;
      const doc = new jsPDF('landscape');
      doc.setFontSize(14);
      doc.text("Weekly Class Timetable", 140, 20, { align: 'center' });

      const headers = [["Day", "Subject 1", "Subject 2", "Subject 3", "Subject 4", "Subject 5", "Subject 6"]];
      const data = [];

      days.forEach(day => {
        const row = [day];
        for (let i = 1; i <= 6; i++) {
          const sub = form[`${day}_subject${i}`].value || ".";
          const time = form[`${day}_time${i}`].value || ".";
          row.push(`${sub} (${time})`);
        }
        data.push(row);
      });

      doc.autoTable({
        head: headers,
        body: data,
        startY: 30,
        theme: 'grid',
        styles: { fontSize: 10 },
        headStyles: { fillColor: [31, 140, 255] },
        alternateRowStyles: { fillColor: [245, 245, 245] }
      });

      doc.save("Weekly_Timetable.pdf");
    });
  </script>

  <!-- jsPDF plugin for table rendering -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.28/jspdf.plugin.autotable.min.js"></script>
</body>
</html>
