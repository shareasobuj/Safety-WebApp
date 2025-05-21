# Safety-WebApp


<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title> Safety WebApp </title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f4f4f9;
      padding: 30px;
    }
    h1 {
      color: #b30000;
      text-align: center;
    }
    input, textarea, button {
      display: block;
      width: 100%;
      padding: 12px;
      margin: 10px 0;
      font-size: 16px;
      border-radius: 5px;
      border: 1px solid #ccc;
    }
    button {
      background: #b30000;
      color: white;
      border: none;
      cursor: pointer;
    }
    button:hover {
      background: #900;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 30px;
    }
    th, td {
      padding: 10px;
      border: 1px solid #999;
      text-align: center;
    }
    th {
      background: #ddd;
    }
  </style>
</head>
<body>

<h1> Safety WebApp </h1>

<input type="text" id="name" placeholder="Full Name" required />
<input type="text" id="mobile" placeholder="Mobile Number" required />
<textarea id="message" rows="2" placeholder="Emergency Message" required></textarea>
<button onclick="getLocationAndSave()">Send SOS</button>
<button onclick="downloadExcel()">Download Excel File</button>

<table id="dataTable">
  <thead>
    <tr>
      <th>Name</th>
      <th>Mobile</th>
      <th>Message</th>
      <th>Location</th>
      <th>Time</th>
    </tr>
  </thead>
  <tbody></tbody>
</table>

<script>
  const dataTable = document.getElementById("dataTable").querySelector("tbody");

  function getLocationAndSave() {
    const name = document.getElementById("name").value.trim();
    const mobile = document.getElementById("mobile").value.trim();
    const message = document.getElementById("message").value.trim();

    if (!name || !mobile || !message) {
      alert("Please fill all fields.");
      return;
    }

    navigator.geolocation.getCurrentPosition(async (pos) => {
      const lat = pos.coords.latitude;
      const lon = pos.coords.longitude;
      const time = new Date().toLocaleString();

      // Reverse geocoding (get address)
      let address = `Lat: ${lat.toFixed(4)}, Lon: ${lon.toFixed(4)}`;
      try {
        const res = await fetch(`https://nominatim.openstreetmap.org/reverse?lat=${lat}&lon=${lon}&format=json`);
        const data = await res.json();
        if (data.display_name) {
          address = data.display_name;
        }
      } catch (err) {
        console.warn("Location name not found.");
      }

      const entry = { name, mobile, message, location: address, time };

      // Save to localStorage
      let logs = JSON.parse(localStorage.getItem("helpRequests") || "[]");
      logs.push(entry);
      localStorage.setItem("helpRequests", JSON.stringify(logs));

      // Show in table
      addRow(entry);
    }, () => {
      alert("Location permission denied.");
    });
  }

  function addRow(entry) {
    const row = document.createElement("tr");
    row.innerHTML = `
      <td>${entry.name}</td>
      <td>${entry.mobile}</td>
      <td>${entry.message}</td>
      <td>${entry.location}</td>
      <td>${entry.time}</td>
    `;
    dataTable.appendChild(row);
  }

  function loadSavedData() {
    const saved = JSON.parse(localStorage.getItem("helpRequests") || "[]");
    saved.forEach(addRow);
  }

  function downloadExcel() {
    const saved = J
    
