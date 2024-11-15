
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
            width: 300px;
        }
        h1 {
            color: #333;
        }
        .input-container {
            display: flex;
            margin-bottom: 20px;
        }
        input[type="text"] {
            width: 100%;
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
    </style>
</head>
<body>
    <div class="container">
        <h1>Task Management Application</h1>
        <div class="input-container">
            <input type="text" id="taskInput" placeholder="Add a new task">
            <button onclick="addTask()">Add Task</button>
        </div>
        <ul id="taskList"></ul>
    </div>

    <script>
        let tasks = [];

        // Function to add a task
        function addTask() {
            const content = document.getElementById('taskInput').value;
            if (!content) return;

            const newTask = { content: content, completed: false };
            tasks.push(newTask);
            renderTasks();
            document.getElementById('taskInput').value = '';
        }

        // Function to delete a task
        function deleteTask(index) {
            tasks.splice(index, 1);
            renderTasks();
        }

        // Function to toggle task completion
        function toggleTask(index) {
            tasks[index].completed = !tasks[index].completed;
            renderTasks();
        }

        // Function to render tasks to the page
        function renderTasks() {
            const taskList = document.getElementById('taskList');
            taskList.innerHTML = '';

            tasks.forEach((task, index) => {
                const li = document.createElement('li');
                li.className = task.completed ? 'completed' : '';
                li.innerHTML = `
                    <span onclick="toggleTask(${index})">${task.content}</span>
                    <button onclick="deleteTask(${index})">Delete</button>
                `;
                taskList.appendChild(li);
            });
        }
    </script>
</body>
</html>
