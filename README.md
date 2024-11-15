from flask import Flask, request, jsonify, render_template_string
import json
import os

app = Flask(__name__)

# HTML, CSS, and JavaScript as one string to serve as the template
html_template = """
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
        <ul id="taskList">
            {% for task in tasks %}
            <li class="{{ 'completed' if task.completed else '' }}">
                <span onclick="toggleTask({{ loop.index0 }})">{{ task.content }}</span>
                <button onclick="deleteTask({{ loop.index0 }})">Delete</button>
            </li>
            {% endfor %}
        </ul>
    </div>

    <script>
        async function addTask() {
            const content = document.getElementById('taskInput').value;
            if (!content) return;
            
            const response = await fetch('/add', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ content: content })
            });
            const tasks = await response.json();
            renderTasks(tasks);
            document.getElementById('taskInput').value = '';
        }

        async function deleteTask(index) {
            const response = await fetch('/delete', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ index: index })
            });
            const tasks = await response.json();
            renderTasks(tasks);
        }

        async function toggleTask(index) {
            const response = await fetch('/toggle', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ index: index })
            });
            const tasks = await response.json();
            renderTasks(tasks);
        }

        function renderTasks(tasks) {
            const taskList = document.getElementById('taskList');
            taskList.innerHTML = '';
            tasks.forEach((task, index) => {
                const li = document.createElement('li');
                li.className = task.completed ? 'completed' : '';
                li.innerHTML = `<span onclick="toggleTask(${index})">${task.content}</span>
                                <button onclick="deleteTask(${index})">Delete</button>`;
                taskList.appendChild(li);
            });
        }
    </script>
</body>
</html>
"""

# Load tasks from a JSON file
def load_tasks():
    if os.path.exists('tasks.json'):
        with open('tasks.json', 'r') as file:
            return json.load(file)
    return []

# Save tasks to a JSON file
def save_tasks(tasks):
    with open('tasks.json', 'w') as file:
        json.dump(tasks, file)

@app.route('/')
def home():
    tasks = load_tasks()
    return render_template_string(html_template, tasks=tasks)

@app.route('/add', methods=['POST'])
def add_task():
    task_content = request.json['content']
    tasks = load_tasks()
    tasks.append({"content": task_content, "completed": False})
    save_tasks(tasks)
    return jsonify(tasks)

@app.route('/delete', methods=['POST'])
def delete_task():
    task_index = int(request.json['index'])
    tasks = load_tasks()
    tasks.pop(task_index)
    save_tasks(tasks)
    return jsonify(tasks)

@app.route('/toggle', methods=['POST'])
def toggle_task():
    task_index = int(request.json['index'])
    tasks = load_tasks()
    tasks[task_index]["completed"] = not tasks[task_index]["completed"]
    save_tasks(tasks)
    return jsonify(tasks)

if __name__ == '__main__':
    app.run(debug=True)
