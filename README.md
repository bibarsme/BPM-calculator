<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Sidings BP Average Calculator</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    body {
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
      max-width: 900px;
      margin: 2rem auto;
      padding: 0 1rem;
      line-height: 1.5;
    }
    h1 {
      font-size: 1.6rem;
      margin-bottom: 0.5rem;
    }
    .subtitle {
      font-size: 0.9rem;
      color: #444;
      margin-bottom: 1rem;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-bottom: 1rem;
    }
    th, td {
      border: 1px solid #ccc;
      padding: 0.4rem;
      text-align: center;
      font-size: 0.9rem;
    }
    th {
      background: #f5f5f5;
    }
    input[type="number"] {
      width: 100%;
      box-sizing: border-box;
      padding: 0.25rem;
    }
    button {
      padding: 0.5rem 1rem;
      margin-right: 0.5rem;
      border: 1px solid #444;
      background: #f5f5f5;
      cursor: pointer;
    }
    button:hover {
      background: #e5e5e5;
    }
    #results {
      margin-top: 1rem;
      padding: 0.75rem;
      border-radius: 4px;
      border: 1px solid #ccc;
      background: #fafafa;
      font-size: 0.95rem;
    }
    .small-print {
      margin-top: 1rem;
      font-size: 0.8rem;
      color: #555;
    }
  </style>
</head>
<body>
  <h1>Sidings Medical Practice – BP Average Calculator</h1>
  <div class="subtitle">
    Enter home or clinic readings. This tool only calculates numerical averages.
    Interpretation must be done by a clinician using NICE NG136.
  </div>

  <table>
    <thead>
      <tr>
        <th>#</th>
        <th>Systolic (mmHg)</th>
        <th>Diastolic (mmHg)</th>
        <th>Heart rate (optional)</th>
        <th>Remove</th>
      </tr>
    </thead>
    <tbody id="bp-body">
      <!-- Rows inserted by JavaScript -->
    </tbody>
  </table>

  <button id="add-row">Add reading</button>
  <button id="calculate">Calculate average</button>

  <div id="results">No readings entered yet.</div>

  <div class="small-print">
    Do not enter patient names, NHS numbers or other identifiers. This tool is for
    internal use only and does not save data to a server.
  </div>

  <script>
    const body = document.getElementById("bp-body");
    const addRowButton = document.getElementById("add-row");
    const calculateButton = document.getElementById("calculate");
    const resultsDiv = document.getElementById("results");

    function createRow(index) {
      const tr = document.createElement("tr");

      const idxCell = document.createElement("td");
      idxCell.textContent = index;
      tr.appendChild(idxCell);

      const sysCell = document.createElement("td");
      const sysInput = document.createElement("input");
      sysInput.type = "number";
      sysInput.min = "0";
      sysInput.step = "1";
      sysInput.placeholder = "e.g. 135";
      sysCell.appendChild(sysInput);
      tr.appendChild(sysCell);

      const diaCell = document.createElement("td");
      const diaInput = document.createElement("input");
      diaInput.type = "number";
      diaInput.min = "0";
      diaInput.step = "1";
      diaInput.placeholder = "e.g. 85";
      diaCell.appendChild(diaInput);
      tr.appendChild(diaCell);

      const hrCell = document.createElement("td");
      const hrInput = document.createElement("input");
      hrInput.type = "number";
      hrInput.min = "0";
      hrInput.step = "1";
      hrInput.placeholder = "optional";
      hrCell.appendChild(hrInput);
      tr.appendChild(hrCell);

      const removeCell = document.createElement("td");
      const removeButton = document.createElement("button");
      removeButton.type = "button";
      removeButton.textContent = "X";
      removeButton.addEventListener("click", () => {
        tr.remove();
        renumberRows();
      });
      removeCell.appendChild(removeButton);
      tr.appendChild(removeCell);

      return tr;
    }

    function renumberRows() {
      Array.from(body.querySelectorAll("tr")).forEach((row, index) => {
        row.firstChild.textContent = index + 1;
      });
    }

    function addRow() {
      const index = body.querySelectorAll("tr").length + 1;
      const row = createRow(index);
      body.appendChild(row);
    }

    function calculateAverage() {
      let sumSys = 0;
      let sumDia = 0;
      let sumHr = 0;
      let count = 0;
      let hrCount = 0;

      const rows = Array.from(body.querySelectorAll("tr"));
      rows.forEach(row => {
        const inputs = row.querySelectorAll("input");
        const sys = parseFloat(inputs[0].value);
        const dia = parseFloat(inputs[1].value);
        const hr = parseFloat(inputs[2].value);

        if (!isNaN(sys) && !isNaN(dia)) {
          sumSys += sys;
          sumDia += dia;
          count += 1;
        }
        if (!isNaN(hr)) {
          sumHr += hr;
          hrCount += 1;
        }
      });

      if (count === 0) {
        resultsDiv.textContent = "Please enter at least one complete systolic/diastolic reading.";
        return;
      }

      const avgSys = (sumSys / count).toFixed(1);
      const avgDia = (sumDia / count).toFixed(1);
      let message = `Number of BP readings: ${count}\n`;
      message += `Average systolic: ${avgSys} mmHg\n`;
      message += `Average diastolic: ${avgDia} mmHg`;

      if (hrCount > 0) {
        const avgHr = (sumHr / hrCount).toFixed(1);
        message += `\nAverage heart rate (where recorded): ${avgHr} bpm`;
      }

      message += `\n\nClinical interpretation should follow NICE NG136 and local ICB guidance.`;

      resultsDiv.textContent = message;
    }

    // Initial rows
    for (let i = 0; i < 3; i++) {
      addRow();
    }

    addRowButton.addEventListener("click", addRow);
    calculateButton.addEventListener("click", calculateAverage);
  </script>
</body>
</html>
