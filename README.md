<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Task Manager</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background-color: #f5f5f5;
        }
        .container {
            text-align: center;
            width: 350px;
        }
        h1 {
            color: #333;
        }
        .input-container {
            display: flex;
            margin-bottom: 20px;
        }
        input[type="text"] {
            width: 80%;
            padding: 8px;
            border: 1px solid #ddd;
            border-radius: 4px;
            margin-right: 5px;
        }
        button {
            padding: 8px;
            border: none;
            background-color: #007bff;
            color: white;
            border-radius: 4px;
            cursor: pointer;
        }
        button:hover {
            background-color: #0056b3;
        }
        select {
            padding: 8px;
            border-radius: 4px;
            border: 1px solid #ddd;
        }
        ul {
            list-style-type: none;
            padding: 0;
        }
        li {
            padding: 10px;
            background-color: #fff;
            border: 1px solid #ddd;
            border-radius: 4px;
            margin-bottom: 5px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        li.completed span {
            text-decoration: line-through;
            color: #888;
        }
        span {
            cursor: pointer;
        }
        .priority {
            font-weight: bold;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1></h1>
        <div class="input-container">
            <input type="text" id="taskInput" placeholder="Add a new task">
            <select id="prioritySelect">
                <option value="low">Low</option>
                <option value="medium">Medium</option>
                <option value="high">High</option>
            </select>
            <button onclick="addTask()">Add Task</button>
        </div>
        
        <div>
            <button onclick="filterTasks('all')">All</button>
            <button onclick="filterTasks('active')">Active</button>
            <button onclick="filterTasks('completed')">Completed</button>
        </div>
        
        <ul id="taskList"></ul>
    </div>

    <script>
        let tasks = JSON.parse(localStorage.getItem('tasks')) || [];
        let currentFilter = 'all'; // 'all', 'active', or 'completed'

        // Function to add a task
        function addTask() {
            const content = document.getElementById('taskInput').value;
            const priority = document.getElementById('prioritySelect').value;
            if (!content) return;

            const newTask = { content: content, completed: false, priority: priority };
            tasks.push(newTask);
            saveTasks();
            renderTasks();
            document.getElementById('taskInput').value = '';
        }

        // Function to delete a task
        function deleteTask(index) {
            tasks.splice(index, 1);
            saveTasks();
            renderTasks();
        }

        // Function to toggle task completion
        function toggleTask(index) {
            tasks[index].completed = !tasks[index].completed;
            saveTasks();
            renderTasks();
        }

        // Function to edit a task
        function editTask(index) {
            const newContent = prompt("Edit Task:", tasks[index].content);
            if (newContent !== null && newContent.trim() !== "") {
                tasks[index].content = newContent;
                saveTasks();
                renderTasks();
            }
        }

        // Function to render tasks based on the current filter
        function renderTasks() {
            const taskList = document.getElementById('taskList');
            taskList.innerHTML = '';

            const filteredTasks = tasks.filter(task => {
                if (currentFilter === 'active') return !task.completed;
                if (currentFilter === 'completed') return task.completed;
                return true; // 'all' filter
            });

            filteredTasks.forEach((task, index) => {
                const li = document.createElement('li');
                li.className = task.completed ? 'completed' : '';
                li.innerHTML = `
                    <span onclick="toggleTask(${index})">${task.content}</span>
                    <span class="priority" style="color: ${getPriorityColor(task.priority)}">${task.priority.charAt(0).toUpperCase() + task.priority.slice(1)}</span>
                    <button onclick="editTask(${index})">Edit</button>
                    <button onclick="deleteTask(${index})">Delete</button>
                `;
                taskList.appendChild(li);
            });
        }

        // Function to get the color for the task's priority
        function getPriorityColor(priority) {
            switch (priority) {
                case 'high': return 'red';
                case 'medium': return 'orange';
                case 'low': return 'green';
                default: return 'black';
            }
        }

        // Function to filter tasks based on status
        function filterTasks(status) {
            currentFilter = status;
            renderTasks();
        }

        // Function to save tasks to local storage
        function saveTasks() {
            localStorage.setItem('tasks', JSON.stringify(tasks));
        }

        // Initial render of tasks
        renderTasks();
    </script>
</body>
</html>
