# focusflow-productivity-dashboard
a productivity dashboard with task management and pomodoro timer


<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>FocusFlow | Productivity</title>

<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: Arial, sans-serif;
}

body {
    background: #101322;
    color: white;
    padding: 30px;
}

.container {
    max-width: 850px;
    margin: auto;
}

header {
    margin-bottom: 30px;
}

header h1 {
    color: #8b83ff;
    font-size: 32px;
}

header p {
    color: #aaa;
    margin-top: 8px;
}

.stats {
    display: flex;
    gap: 15px;
    margin-bottom: 25px;
}

.card {
    background: #1c2135;
    padding: 20px;
    border-radius: 12px;
    flex: 1;
}

.card h2 {
    color: #8b83ff;
    font-size: 28px;
    margin-top: 10px;
}

input, button {
    padding: 12px;
    border: none;
    border-radius: 7px;
    font-size: 15px;
}

input {
    background: #252b40;
    color: white;
    flex: 1;
    min-width: 0;
}

button {
    background: #7168ef;
    color: white;
    cursor: pointer;
}

button:hover {
    background: #8b83ff;
}

.add-task {
    display: flex;
    gap: 10px;
    margin-bottom: 25px;
}

.task {
    background: #1c2135;
    padding: 15px;
    margin-bottom: 10px;
    border-radius: 9px;
    display: flex;
    align-items: center;
    gap: 10px;
}

.task span {
    flex: 1;
    overflow-wrap: anywhere;
}

.done {
    text-decoration: line-through;
    color: #777;
}

.delete {
    background: #e34b64;
}

.timer {
    background: #1c2135;
    padding: 25px;
    border-radius: 12px;
    text-align: center;
    margin-top: 25px;
}

#time {
    font-size: 50px;
    margin: 15px;
    color: #8b83ff;
}

.timer button {
    margin: 5px;
}

@media(max-width: 550px) {
    body { padding: 15px; }
    .stats { flex-direction: column; }
}
</style>
</head>

<body>
<div class="container">

<header>
    <h1>FocusFlow ⚡</h1>
    <p>Your personal productivity dashboard</p>
</header>

<div class="stats">
    <div class="card">
        Total Tasks
        <h2 id="total">0</h2>
    </div>

    <div class="card">
        Completed
        <h2 id="completed">0</h2>
    </div>

    <div class="card">
        Progress
        <h2 id="progress">0%</h2>
    </div>
</div>

<h3>My Tasks</h3>
<br>

<div class="add-task">
    <input id="taskInput"
           placeholder="Enter your task...">
    <button onclick="addTask()">Add +</button>
</div>

<div id="taskList"></div>

<div class="timer">
    <h3>Pomodoro Focus Timer</h3>
    <div id="time">25:00</div>

    <button onclick="startTimer()">Start</button>
    <button onclick="pauseTimer()">Pause</button>
    <button onclick="resetTimer()">Reset</button>
</div>

</div>

<script>
let tasks = JSON.parse(
    localStorage.getItem("tasks")
) || [];

let seconds = 25 * 60;
let interval = null;

function saveTasks() {
    localStorage.setItem(
        "tasks",
        JSON.stringify(tasks)
    );
}

function addTask() {
    let input = document.getElementById("taskInput");
    let text = input.value.trim();

    if (text === "") {
        alert("Please enter a task!");
        return;
    }

    tasks.push({
        id: Date.now(),
        name: text,
        completed: false
    });

    input.value = "";
    saveTasks();
    displayTasks();
}

function toggleTask(id) {
    tasks = tasks.map(task =>
        task.id === id
        ? {...task, completed: !task.completed}
        : task
    );

    saveTasks();
    displayTasks();
}

function deleteTask(id) {
    tasks = tasks.filter(task => task.id !== id);
    saveTasks();
    displayTasks();
}

function displayTasks() {
    let list = document.getElementById("taskList");

    list.innerHTML = "";

    tasks.forEach(task => {
        let div = document.createElement("div");
        div.className = "task";

        let checkbox = document.createElement("input");
        checkbox.type = "checkbox";
        checkbox.checked = task.completed;
        checkbox.style.flex = "0 0 auto";
        checkbox.addEventListener("change", () => {
            toggleTask(task.id);
        });

        let span = document.createElement("span");
        span.textContent = task.name;
        if (task.completed) span.className = "done";

        let button = document.createElement("button");
        button.textContent = "Delete";
        button.className = "delete";
        button.addEventListener("click", () => {
            deleteTask(task.id);
        });

        div.append(checkbox, span, button);
        list.appendChild(div);
    });

    let done = tasks.filter(t => t.completed).length;
    let total = tasks.length;

    document.getElementById("total").textContent = total;
    document.getElementById("completed").textContent = done;

    document.getElementById("progress").textContent =
        total ? Math.round(done / total * 100) + "%" : "0%";
}

function updateTimer() {
    let min = Math.floor(seconds / 60);
    let sec = seconds % 60;

    document.getElementById("time").textContent =
        String(min).padStart(2, "0") + ":" +
        String(sec).padStart(2, "0");
}

function startTimer() {
    if (interval) return;

    interval = setInterval(() => {
        if (seconds > 0) {
            seconds--;
            updateTimer();
        } else {
            clearInterval(interval);
            interval = null;
            alert("Focus session completed! 🎉");
        }
    }, 1000);
}

function pauseTimer() {
    clearInterval(interval);
    interval = null;
}

function resetTimer() {
    pauseTimer();
    seconds = 25 * 60;
    updateTimer();
}

displayTasks();
updateTimer();
</script>

</body>
</html>
