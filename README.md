
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Bank Queue Management System</title>

  <style>
    * {
      box-sizing: border-box;
    }

    body {
      margin: 0;
      font-family: Arial, sans-serif;
      background: #f1f5f9;
      color: #1e293b;
    }

    header {
      background: #073b70;
      color: white;
      padding: 24px 16px;
      text-align: center;
    }

    header h1 {
      margin: 0 0 8px;
    }

    .container {
      max-width: 1000px;
      margin: 25px auto;
      padding: 0 16px;
    }

    .dashboard {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 15px;
      margin-bottom: 20px;
    }

    .card {
      background: white;
      padding: 20px;
      border-radius: 12px;
      box-shadow: 0 3px 10px #0000000d;
    }

    .stat {
      text-align: center;
    }

    .stat h2 {
      color: #0756a0;
      font-size: 30px;
      margin: 10px 0;
    }

    .section-title {
      margin-top: 0;
    }

    form {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 12px;
    }

    input, select, button {
      padding: 12px;
      border-radius: 7px;
      font-size: 15px;
    }

    input, select {
      width: 100%;
      border: 1px solid #cbd5e1;
      background: white;
    }

    button {
      border: none;
      cursor: pointer;
      font-weight: bold;
    }

    button:hover {
      opacity: 0.9;
    }

    .primary {
      background: #0756a0;
      color: white;
    }

    .success {
      background: #15803d;
      color: white;
    }

    .warning {
      background: #d97706;
      color: white;
    }

    .danger {
      background: #dc2626;
      color: white;
    }

    .full {
      grid-column: 1 / -1;
    }

    .current-number {
      font-size: 42px;
      color: #0756a0;
      font-weight: bold;
      margin: 12px 0;
    }

    .actions {
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
      margin-top: 15px;
    }

    .actions button {
      flex: 1;
      min-width: 130px;
    }

    .table-wrap {
      overflow-x: auto;
    }

    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 10px;
    }

    th, td {
      text-align: left;
      padding: 12px 10px;
      border-bottom: 1px solid #e2e8f0;
    }

    th {
      background: #f8fafc;
    }

    .status {
      display: inline-block;
      padding: 5px 9px;
      border-radius: 20px;
      background: #fef3c7;
      color: #92400e;
      font-size: 12px;
    }

    .empty {
      text-align: center;
      padding: 25px;
      color: #64748b;
    }

    footer {
      text-align: center;
      padding: 20px;
      color: #64748b;
    }

    @media (max-width: 600px) {
      .dashboard {
        grid-template-columns: 1fr;
      }

      form {
        grid-template-columns: 1fr;
      }

      .full {
        grid-column: auto;
      }
    }
  </style>
</head>

<body>
  <header>
    <h1>Bank Queue Management</h1>
    <p>Customer Service and Queue System</p>
  </header>

  <main class="container">

    <section class="dashboard">
      <div class="card stat">
        <p>Now Serving</p>
        <h2 id="nowServing">---</h2>
      </div>

      <div class="card stat">
        <p>Customers Waiting</p>
        <h2 id="waitingCount">0</h2>
      </div>

      <div class="card stat">
        <p>Customers Served</p>
        <h2 id="servedCount">0</h2>
      </div>
    </section>

    <section class="card">
      <h2 class="section-title">Register a Customer</h2>

      <form id="customerForm">
        <input
          type="text"
          id="customerName"
          placeholder="Customer name"
          required
          maxlength="80"
        >

        <select id="serviceType" required>
          <option value="">Select banking service</option>
          <option value="Deposit">Deposit</option>
          <option value="Withdrawal">Withdrawal</option>
          <option value="Transfer">Transfer</option>
          <option value="Account Opening">Account Opening</option>
          <option value="Customer Support">Customer Support</option>
        </select>

        <button class="primary full" type="submit">
          Add Customer to Queue
        </button>
      </form>
    </section>

    <section class="card" style="margin-top: 20px;">
      <h2 class="section-title">Teller Control Panel</h2>

      <p>Current customer:</p>
      <div class="current-number" id="currentTicket">
        No customer
      </div>

      <p id="currentDetails">
        Call the next customer to begin.
      </p>

      <div class="actions">
        <button class="primary" id="nextBtn">
          Call Next Customer
        </button>

        <button class="success" id="serveBtn">
          Mark as Served
        </button>

        <button class="warning" id="skipBtn">
          Skip Customer
        </button>

        <button class="danger" id="resetBtn">
          Reset Queue
        </button>
      </div>
    </section>

    <section class="card" style="margin-top: 20px;">
      <h2 class="section-title">Waiting List</h2>

      <div class="table-wrap">
        <table>
          <thead>
            <tr>
              <th>Queue No.</th>
              <th>Customer</th>
              <th>Service</th>
              <th>Status</th>
            </tr>
          </thead>

          <tbody id="queueTable"></tbody>
        </table>
      </div>
    </section>

    <section class="card" style="margin-top: 20px;">
      <h2 class="section-title">Service History</h2>

      <div class="table-wrap">
        <table>
          <thead>
            <tr>
              <th>Queue No.</th>
              <th>Customer</th>
              <th>Service</th>
              <th>Status</th>
            </tr>
          </thead>

          <tbody id="historyTable"></tbody>
        </table>
      </div>
    </section>
  </main>

  <footer>
    Bank Queue Management System | Demonstration Project
  </footer>

  <script>
    // Queue data structure: FIFO
    let queue = [];
    let currentCustomer = null;
    let history = [];
    let nextNumber = 1;
    let servedCount = 0;

    const customerForm = document.getElementById("customerForm");
    const customerName = document.getElementById("customerName");
    const serviceType = document.getElementById("serviceType");

    // Add a customer to the rear of the queue.
    customerForm.addEventListener("submit", function(event) {
      event.preventDefault();

      const name = customerName.value.trim();
      const service = serviceType.value;

      if (!name || !service) {
        alert("Please enter a name and select a service.");
        return;
      }

      const customer = {
        number: nextNumber++,
        name: name,
        service: service,
        status: "Waiting"
      };

      queue.push(customer);

      customerForm.reset();
      customerName.focus();

      render();
    });

    // Remove the first waiting customer from the queue.
    document.getElementById("nextBtn").addEventListener("click", function() {
      if (currentCustomer) {
        alert("Please serve or skip the current customer first.");
        return;
      }

      if (queue.length === 0) {
        alert("There are no customers waiting.");
        return;
      }

      currentCustomer = queue.shift();
      currentCustomer.status = "Being Served";

      render();
    });

    // Complete service for the current customer.
    document.getElementById("serveBtn").addEventListener("click", function() {
      if (!currentCustomer) {
        alert("There is no customer currently being served.");
        return;
      }

      currentCustomer.status = "Served";
      history.unshift(currentCustomer);
      servedCount++;
      currentCustomer = null;

      render();
    });

    // Return a skipped customer to the rear of the queue.
    document.getElementById("skipBtn").addEventListener("click", function() {
      if (!currentCustomer) {
        alert("There is no customer to skip.");
        return;
      }

      currentCustomer.status = "Skipped — Requeued";
      currentCustomer = null;

      // FIFO queue: reinsert at the rear.
      queue.push({
        ...historyCustomerPlaceholder(),
        ...skippedCustomer()
      });

      render();

      function historyCustomerPlaceholder() {
        return {};
      }

      function skippedCustomer() {
        // The skipped customer's details are preserved below.
        return {};
      }
    });

    // Reset the demo after confirmation.
    document.getElementById("resetBtn").addEventListener("click", function() {
      if (!confirm("Clear the entire queue and history?")) {
        return;
      }

      queue = [];
      currentCustomer = null;
      history = [];
      nextNumber = 1;
      servedCount = 0;

      render();
    });

    function render() {
      document.getElementById("nowServing").textContent =
        currentCustomer
          ? formatNumber(currentCustomer.number)
          : "---";

      document.getElementById("waitingCount").textContent = queue.length;
      document.getElementById("servedCount").textContent = servedCount;

      document.getElementById("currentTicket").textContent =
        currentCustomer
          ? formatNumber(currentCustomer.number)
          : "No customer";

      document.getElementById("currentDetails").textContent =
        currentCustomer
          ? currentCustomer.name + " — " + currentCustomer.service
          : "Call the next customer to begin.";

      const queueTable = document.getElementById("queueTable");

      queueTable.replaceChildren();

      if (queue.length === 0) {
        showEmpty(queueTable, "No customers are waiting.");
      } else {
        queue.forEach(function(customer) {
          queueTable.appendChild(makeRow(customer));
        });
      }

      const historyTable = document.getElementById("historyTable");

      historyTable.replaceChildren();

      if (history.length === 0) {
        showEmpty(historyTable, "No service history yet.");
      } else {
        history.forEach(function(customer) {
          historyTable.appendChild(makeRow(customer));
        });
      }
    }

    function formatNumber(number) {
      return "A" + String(number).padStart(3, "0");
    }

    function makeRow(customer) {
      const row = document.createElement("tr");

      [
        formatNumber(customer.number),
        customer.name,
        customer.service,
        customer.status
      ].forEach(function(value, index) {
        const cell = document.createElement("td");

        // Use textContent to display customer input safely.
        cell.textContent = value;

        if (index === 3) {
          const badge = document.createElement("span");
          badge.className = "status";
          badge.textContent = value;
          cell.replaceChildren(badge);
        }

        row.appendChild(cell);
      });

      return row;
    }

    function showEmpty(table, message) {
      const row = document.createElement("tr");
      const cell = document.createElement("td");

      cell.colSpan = 4;
      cell.className = "empty";
      cell.textContent = message;

      row.appendChild(cell);
      table.appendChild(row);
    }

    render();
  </script>
</body>
</html>
