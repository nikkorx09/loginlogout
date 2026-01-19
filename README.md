<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Employee Time System</title>

<style>
body {
  font-family: Arial, sans-serif;
  background: #f4f6f8;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
.box {
  background: #fff;
  padding: 20px;
  width: 360px;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0,0,0,.1);
}
.hidden { display: none; }
button { width: 100%; padding: 8px; margin-top: 8px; }
input, select, textarea {
  width: 100%;
  padding: 8px;
  margin-top: 6px;
}
h3 { margin-top: 0; }
.log {
  font-size: 13px;
  background: #eee;
  padding: 6px;
  margin-top: 5px;
}
</style>
</head>

<body>

<!-- LOGIN -->
<div class="box" id="loginBox">
  <h3>Login</h3>
  <input id="username" placeholder="Username">
  <input id="password" type="password" placeholder="Password">
  <button onclick="login()">Login</button>
  <p id="error" style="color:red;"></p>
</div>

<!-- EMPLOYEE DASHBOARD -->
<div class="box hidden" id="employeeBox">
  <h3>Employee Dashboard</h3>
  <p id="empName"></p>
  <button onclick="punch('IN')">Punch In</button>
  <button onclick="punch('OUT')">Punch Out</button>
  <h4>My Schedule</h4>
  <div id="mySchedule"></div>
  <button onclick="logout()">Logout</button>
</div>

<!-- ADMIN DASHBOARD -->
<div class="box hidden" id="adminBox">
  <h3>Admin Dashboard</h3>

  <button onclick="punch('IN')">Punch In</button>
  <button onclick="punch('OUT')">Punch Out</button>

  <h4>Upload Schedule</h4>
  <select id="schedUser"></select>
  <textarea id="schedText" placeholder="Example: Mon–Fri 8AM–12PM"></textarea>
  <button onclick="saveSchedule()">Save Schedule</button>

  <h4>Employee Logs</h4>
  <div id="logs"></div>

  <button onclick="logout()">Logout</button>
</div>

<script>
// USERS (DEMO ONLY)
const users = {
  admin: { password: "admin123", role: "admin" },
  nikko: { password: "1234", role: "employee" },
  va1: { password: "pass", role: "employee" }
};

// STORAGE INIT
let logs = JSON.parse(localStorage.getItem("logs")) || [];
let schedules = JSON.parse(localStorage.getItem("schedules")) || {};

function login() {
  const u = username.value;
  const p = password.value;

  if (!users[u] || users[u].password !== p) {
    error.innerText = "Invalid login";
    return;
  }

  localStorage.setItem("currentUser", u);
  loadApp();
}

function loadApp() {
  const u = localStorage.getItem("currentUser");
  if (!u) return;

  loginBox.classList.add("hidden");

  if (users[u].role === "admin") {
    adminBox.classList.remove("hidden");
    loadAdmin();
  } else {
    employeeBox.classList.remove("hidden");
    empName.innerText = "Welcome, " + u;
    mySchedule.innerText = schedules[u] || "No schedule yet.";
  }
}

function logout() {
  localStorage.removeItem("currentUser");
  location.reload();
}

function punch(type) {
  const user = localStorage.getItem("currentUser");
  logs.push({
    user,
    type,
    time: new Date().toLocaleString()
  });
  localStorage.setItem("logs", JSON.stringify(logs));
  alert(`Punched ${type}`);
  loadAdmin();
}

function loadAdmin() {
  // Load users
  schedUser.innerHTML = "";
  Object.keys(users).forEach(u => {
    if (users[u].role === "employee") {
      schedUser.innerHTML += `<option>${u}</option>`;
    }
  });

  // Load logs
  logsDiv = document.getElementById("logs");
  logsDiv.innerHTML = "";
  logs.forEach(l => {
    logsDiv.innerHTML += `<div class="log">${l.user} - ${l.type} - ${l.time}</div>`;
  });
}

function saveSchedule() {
  schedules[schedUser.value] = schedText.value;
  localStorage.setItem("schedules", JSON.stringify(schedules));
  alert("Schedule saved");
}

// AUTO LOAD
loadApp();
</script>

</body>
</html>
