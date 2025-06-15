<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Inventory of Supplies</title>
  <script src="https://cdn.sheetjs.com/xlsx-latest/package/dist/xlsx.full.min.js"></script>
  <style>
    * { box-sizing: border-box; }
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      display: flex;
      flex-direction: column;
      height: 100vh;
      opacity: 0;
      animation: fadeIn 1s ease forwards;
    }
    @keyframes fadeIn { to { opacity: 1; } }
    #container { width: 100%; flex: 1; }
    aside#sidebar {
      width: 220px;
      background-color: #2f3640;
      color: white;
      padding: 20px;
      height: 100vh;
      position: fixed;
    }
    aside h2 { margin-top: 0; }
    nav ul { list-style: none; padding: 0; }
    nav ul li { margin: 20px 0; }
    nav ul li a,
    .logout-btn {
      color: white;
      text-decoration: none;
      background-color: #353b48;
      border: none;
      padding: 10px;
      width: 100%;
      text-align: left;
      display: block;
      border-radius: 5px;
      cursor: pointer;
      transition: background 0.3s ease;
    }
    nav ul li a.active { background-color: #00a8ff; }
    .logout-btn:hover { background-color: #e84118; }
    #main-content {
      margin-left: 220px;
      padding: 20px;
      flex: 1;
    }
    .stats {
      display: flex;
      flex-wrap: wrap;
      gap: 20px;
    }
    .stat-box {
      background: #dcdde1;
      padding: 20px;
      border-radius: 10px;
      flex: 1 1 30%;
      text-align: center;
    }
    #inventory-form {
      display: flex;
      flex-direction: column;
      gap: 10px;
      margin: 20px 0;
    }
    #inventory-form input,
    #inventory-form select,
    #inventory-form button {
      padding: 10px;
      font-size: 14px;
    }
    .table-actions {
      display: flex;
      gap: 10px;
      margin-bottom: 10px;
    }
    table {
      width: 100%;
      border-collapse: collapse;
    }
    table th, table td {
      border: 1px solid #ccc;
      padding: 10px;
      text-align: center;
    }
    .pagination {
      display: flex;
      justify-content: center;
      margin-top: 10px;
      gap: 10px;
    }
    .pagination button {
      padding: 5px 10px;
    }
    .modal {
      display: none;
      position: fixed;
      top: 0; left: 0;
      width: 100%; height: 100%;
      background: rgba(0, 0, 0, 0.6);
      justify-content: center;
      align-items: center;
    }
    .modal-content {
      background: #fff;
      padding: 20px;
      border-radius: 5px;
      text-align: center;
    }
    .modal input {
      width: 80%;
      padding: 8px;
      margin-top: 10px;
    }
    @media screen and (max-width: 768px) {
      aside#sidebar { width: 180px; }
      #main-content { margin-left: 180px; padding: 10px; }
      .stat-box { flex: 1 1 100%; }
    }
    @media print {
  body * {
    visibility: hidden;
  }
  #inventory-table, #inventory-table * {
    visibility: visible;
  }
  #inventory-table {
    position: absolute;
    left: 0;
    top: 0;
  }
}
  </style>
</head>
<body>
  <div id="container">
    <aside id="sidebar">
      <h2>Inventory System</h2>
      <nav>
        <ul>
          <li><a href="#" id="dashboard-link" class="active">Dashboard</a></li>
          <li><a href="#" id="inventory-link">Inventory</a></li>
        </ul>
      </nav>
    </aside>
    <main id="main-content">
      <section id="dashboard-section">
        <h2>Dashboard Summary</h2>
        <div class="stats">
          <div class="stat-box">
            <h3>Total Requested</h3>
            <p id="total-requested">0</p>
          </div>
          <div class="stat-box">
            <h3>Total Provided</h3>
            <p id="total-provided">0</p>
          </div>
          <div class="stat-box">
            <h3>Total Balance</h3>
            <p id="total-balance">0</p>
          </div>
        </div>
      </section>
      <section id="inventory-section" style="display:none;">
        <h2>Inventory</h2>
        <form id="inventory-form">
          <select id="item" required>
            <option value="">Select Item</option>
            <option value="BATTERY, dry cell, AA, 2 pieces per blister pack">BATTERY, dry cell, AA, 2 pieces per blister pack</option>
            <option value="BATTERY, dry cell, AAA, 2 pieces per blister pack">BATTERY, dry cell, AAA, 2 pieces per blister pack</option>
            <option value="CLEARBOOK, Legal size">CLEARBOOK, Legal size</option>
            <option value="CLIP, backfold, 19mm">CLIP, backfold, 19mm</option>
            <option value="CLIP, backfold, 25mm">CLIP, backfold, 25mm</option>
            <option value="CLIP, backfold, 32mm">CLIP, backfold, 32mm</option>
            <option value="CLIP, backfold, 50mm">CLIP, backfold, 50mm</option>
            <option value="CORRECTION TAPE, film base type, UL 6m min">CORRECTION TAPE, film base type, UL 6m min</option>
            <option value="CUTTER/UTILITY KNIFE, for general purpose">CUTTER/UTILITY KNIFE, for general purpose</option>
            <option value="DATA FILE BOX, made of chipboard, with closed ends, red">DATA FILE BOX, made of chipboard, with closed ends, red</option>
            <option value="FASTENER, 70mm, metal, 500 sets per box">FASTENER, 70mm, metal, 500 sets per box</option>
            <option value="FILE ORGANIZER, expanding, plastic, legal">FILE ORGANIZER, expanding, plastic, legal</option>
            <option value="FOLDER with tab, A4">FOLDER with tab, A4</option>
            <option value="FOLDER with tab, Legal">FOLDER with tab, Legal</option>
            <option value="GLUE, all-purpose, 130grams">GLUE, all-purpose, 130grams</option>
            <option value="MARKER, Permanent, Black">MARKER, Permanent, Black</option>
            <option value="MARKER, Permanent, Blue">MARKER, Permanent, Blue</option>
            <option value="MARKER, Permanent, Red">MARKER, Permanent, Red</option>
            <option value="MARKER, Whiteboard, Black">MARKER, Whiteboard, Black</option>
            <option value="MARKER, Whiteboard, Blue">MARKER, Whiteboard, Blue</option>
            <option value="MARKER, Whiteboard, Red">MARKER, Whiteboard, Red</option>
            <option value="NOTEPAD, stick-on, 76mm x 100mm">NOTEPAD, stick-on, 76mm x 100mm</option>
            <option value="PAPER CLIP, vinly/plastic coated, 33mm">PAPER CLIP, vinly/plastic coated, 33mm</option>
            <option value="PAPER CLIP, vinly/plastic coated, jumbo, 50mm">PAPER CLIP, vinly/plastic coated, jumbo, 50mm</option>
            <option value="PAPER, MULTICOPY A4, 70gsm">PAPER, MULTICOPY A4, 70gsm</option>
            <option value="PAPER, MULTICOPY LEGAL, 70gsm">PAPER, MULTICOPY LEGAL, 70gsm</option>
            <option value="PENCIL, lead/graphite, with eraser, 12 pieces per box">PENCIL, lead/graphite, with eraser, 12 pieces per box</option>
            <option value="Other">Other</option>
          </select>
          <input type="number" id="quantity" placeholder="Quantity" required />
          <input type="number" id="requested" placeholder="Total Requested" required />
          <input type="number" id="provided" placeholder="Monthly/Quarterly Provided" required />
          <button id="addEntryBtn">Add Entry</button>

        </form>
        <input type="text" id="searchInput" placeholder="Search Item..." />
        <div class="table-actions">
          <button onclick="printTable()">Print Table</button>
          <button onclick="exportToExcel()">Export to Excel</button>
        </div>
        <table id="inventory-table">
          <thead>
            <tr>
              <th>Item Name</th>
              <th>Quantity</th>
              <th>Total Requested</th>
              <th>Provided</th>
              <th>Balance</th>
              <th>Actions</th>
            </tr>
          </thead>
          <tbody></tbody>
        </table>
        <div class="pagination" id="pagination"></div>
      </section>
    </main>
  </div>

  <div class="modal" id="otherModal">
    <div class="modal-content">
      <h3>Enter Item Name</h3>
      <input type="text" id="customItem" placeholder="Custom Item Name" />
      <button onclick="confirmOther()">Confirm</button>
    </div>
  </div>

  <script>

  let inventoryData = JSON.parse(localStorage.getItem("inventoryData")) || [];

  function updateTable() {
    const tbody = document.querySelector("#inventory-table tbody");
    tbody.innerHTML = "";
    inventoryData.forEach((item, index) => {
      const row = document.createElement("tr");
      const balance = item.requested - item.provided;
      row.innerHTML = `
        <td>${item.item}</td>
        <td>${item.quantity}</td>
        <td>${item.requested}</td>
        <td>${item.provided}</td>
        <td>${balance}</td>
        <td>
          <button onclick="editEntry(${index})">Edit</button>
          <button onclick="deleteEntry(${index})">Delete</button>
        </td>
      `;
      tbody.appendChild(row);
    });

    // Update dashboard summary
    const totalRequested = inventoryData.reduce((sum, item) => sum + Number(item.requested), 0);
    const totalProvided = inventoryData.reduce((sum, item) => sum + Number(item.provided), 0);
    const totalBalance = totalRequested - totalProvided;
    document.getElementById("total-requested").textContent = totalRequested;
    document.getElementById("total-provided").textContent = totalProvided;
    document.getElementById("total-balance").textContent = totalBalance;
  }

  function saveData() {
    localStorage.setItem("inventoryData", JSON.stringify(inventoryData));
  }

  function clearForm() {
    document.getElementById("item").value = "";
    document.getElementById("quantity").value = "";
    document.getElementById("requested").value = "";
    document.getElementById("provided").value = "";
  }

  document.getElementById("addEntryBtn").addEventListener("click", function (e) {
    e.preventDefault();

    const item = document.getElementById("item").value;
    const quantity = document.getElementById("quantity").value;
    const requested = document.getElementById("requested").value;
    const provided = document.getElementById("provided").value;

    if (!item || !quantity || !requested || !provided) {
      alert("Please complete all fields.");
      return;
    }

    const newEntry = {
      item,
      quantity,
      requested,
      provided
    };

    inventoryData.unshift(newEntry);
    saveData();
    updateTable();
    clearForm();
  });

  function editEntry(index) {
    const entry = inventoryData[index];
    const newItem = prompt("Update item name:", entry.item);
    const newQuantity = prompt("Update quantity:", entry.quantity);
    const newRequested = prompt("Update total requested:", entry.requested);
    const newProvided = prompt("Update provided:", entry.provided);

    if (newItem !== null && newQuantity !== null && newRequested !== null && newProvided !== null) {
      inventoryData[index] = {
        item: newItem,
        quantity: Number(newQuantity),
        requested: Number(newRequested),
        provided: Number(newProvided)
      };
      saveData();
      updateTable();
    }
  }

  function deleteEntry(index) {
    if (confirm("Are you sure you want to delete this entry?")) {
      inventoryData.splice(index, 1);
      saveData();
      updateTable();
    }
  }

  window.addEventListener("DOMContentLoaded", () => {
    updateTable();
  });

  // Modal confirm (for custom item)
  function confirmOther() {
    const custom = document.getElementById("customItem").value.trim();
    if (custom !== "") {
      const option = document.createElement("option");
      option.value = custom;
      option.text = custom;
      option.selected = true;
      document.getElementById("item").appendChild(option);
      document.getElementById("otherModal").style.display = "none";
      document.getElementById("customItem").value = "";
    }
  }

  // Rebind confirmOther to global
  window.confirmOther = confirmOther;

    const totalRequestedMap = {
      "BATTERY, dry cell, AA, 2 pieces per blister pack": 12,
      "BATTERY, dry cell, AAA, 2 pieces per blister pack": 12,
      "CLEARBOOK, Legal size": 4,
      "CLIP, backfold, 19mm": 4,
      "CLIP, backfold, 25mm": 4,
      "CLIP, backfold, 32mm": 4,
      "CLIP, backfold, 50mm": 4,
      "CORRECTION TAPE, film base type, UL 6m min": 16,
      "CUTTER/UTILITY KNIFE, for general purpose": 2,
      "DATA FILE BOX, made of chipboard, with closed ends, red": 8,
      "FASTENER, 70mm, metal, 500 sets per box": 2,
      "FILE ORGANIZER, expanding, plastic, legal": 4,
      "FOLDER with tab, A4": 1,
      "FOLDER with tab, Legal": 2,
      "GLUE, all-purpose, 130grams": 2,
      "MARKER, Permanent, Black": 2,
      "MARKER, Permanent, Blue": 2,
      "MARKER, Permanent, Red": 2,
      "MARKER, Whiteboard, Black": 8,
      "MARKER, Whiteboard, Blue": 8,
      "MARKER, Whiteboard, Red": 4,
      "NOTEPAD, stick-on, 76mm x 100mm": 10,
      "PAPER CLIP, vinly/plastic coated, 33mm": 4,
      "PAPER CLIP, vinly/plastic coated, jumbo, 50mm": 4,
      "PAPER, MULTICOPY A4, 70gsm": 20,
      "PAPER, MULTICOPY LEGAL, 70gsm": 10,
      "PENCIL, lead/graphite, with eraser, 12 pieces per box": 1
    };

    document.getElementById("dashboard-link").addEventListener("click", function(e) {
      e.preventDefault();
      document.getElementById("dashboard-section").style.display = "block";
      document.getElementById("inventory-section").style.display = "none";
      this.classList.add("active");
      document.getElementById("inventory-link").classList.remove("active");
    });

    document.getElementById("inventory-link").addEventListener("click", function(e) {
      e.preventDefault();
      document.getElementById("dashboard-section").style.display = "none";
      document.getElementById("inventory-section").style.display = "block";
      this.classList.add("active");
      document.getElementById("dashboard-link").classList.remove("active");
    });

    document.getElementById("item").addEventListener("change", function () {
      const selectedItem = this.value;
      const requestedInput = document.getElementById("requested");
      if (selectedItem === "Other") {
        document.getElementById("otherModal").style.display = "flex";
        requestedInput.value = "";
      } else {
        requestedInput.value = totalRequestedMap[selectedItem] || "";
      }
    });

    function confirmOther() {
      const custom = document.getElementById("customItem").value.trim();
      if (custom !== "") {
        const option = document.createElement("option");
        option.value = custom;
        option.text = custom;
        option.selected = true;
        document.getElementById("item").appendChild(option);
        document.getElementById("otherModal").style.display = "none";
        document.getElementById("customItem").value = "";
      }
    }
    document.getElementById("addEntryBtn").addEventListener("click", function () {
    const itemName = document.getElementById("itemName").value;
    const totalRequested = document.getElementById("totalRequested").value;
    const quantity = document.getElementById("quantity").value;

    if (!itemName || !quantity) {
        alert("Please select an item and enter quantity.");
        return;
    }

    const newRow = document.createElement("tr");
    newRow.innerHTML = `
        <td>${itemName}</td>
        <td>${totalRequested}</td>
        <td>${quantity}</td>
        <td><button class="editBtn">Edit</button></td>
    `;

    document.getElementById("inventoryTableBody").appendChild(newRow);

    // Clear inputs after adding
    document.getElementById("itemName").value = "";
    document.getElementById("totalRequested").value = "";
    document.getElementById("quantity").value = "";
});
  document.addEventListener("DOMContentLoaded", function () {
  const addEntryBtn = document.getElementById("addEntryBtn");
  const inventoryTableBody = document.querySelector("#inventory-table tbody");

  addEntryBtn.addEventListener("click", function (e) {
    e.preventDefault(); // Prevent form submission

    const itemName = document.getElementById("item").value;
    const quantity = document.getElementById("quantity").value;
    const requested = document.getElementById("requested").value;
    const provided = document.getElementById("provided").value;

    if (!itemName || !quantity || !requested || !provided) {
      alert("Are you sure you want to add it?");
      return;
    }

    const balance = requested - provided;

    const newRow = document.createElement("tr");
    newRow.innerHTML = `
      <td>${itemName}</td>
      <td>${quantity}</td>
      <td>${requested}</td>
      <td>${provided}</td>
      <td>${balance}</td>
      <td><button class="editBtn">Edit</button></td>
    `;
    inventoryTableBody.appendChild(newRow);

    // Clear inputs
    document.getElementById("item").value = "";
    document.getElementById("quantity").value = "";
    document.getElementById("requested").value = "";
    document.getElementById("provided").value = "";
  });
});
  function printTable() {
    const printContents = document.getElementById("inventory-table").outerHTML;
    const originalContents = document.body.innerHTML;

    const printWindow = window.open("", "", "height=600,width=800");
    printWindow.document.write("<html><head><title>Print Inventory Table</title>");
    printWindow.document.write("<style>");
    printWindow.document.write("table { width: 100%; border-collapse: collapse; }");
    printWindow.document.write("th, td { border: 1px solid #000; padding: 8px; text-align: left; }");
    printWindow.document.write("</style>");
    printWindow.document.write("</head><body>");
    printWindow.document.write("<h2>Inventory Table</h2>");
    printWindow.document.write(printContents);
    printWindow.document.write("</body></html>");
    printWindow.document.close();
    printWindow.print();
  }

  function exportToExcel() {
    const table = document.getElementById("inventory-table");
    let csv = [];
    for (let row of table.rows) {
      let rowData = [];
      for (let cell of row.cells) {
        rowData.push(cell.innerText);
      }
      csv.push(rowData.join(","));
    }

    const csvContent = "data:text/csv;charset=utf-8," + csv.join("\n");
    const encodedUri = encodeURI(csvContent);
    const link = document.createElement("a");
    link.setAttribute("href", encodedUri);
    link.setAttribute("download", "inventory_data.csv");
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
  }

  // Rebind to global
  window.printTable = printTable;
  window.exportToExcel = exportToExcel;

  </script>
</body>
</html>
