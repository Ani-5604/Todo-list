<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>To-Do List </title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-image: linear-gradient(to bottom, rgba(91, 68, 58, 0.8), rgba(66, 66, 80, 0.8)), url('todo.jpg' ),url('tdl.jpg');
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }

        .container {
            background-color: #fff;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            width: 100%;
            max-width: 500px;
            padding: 20px;
            text-align: center;
        }

        h1, h2 {
            margin-top: 0;
            color: #070606;
        }

        .input-container {
            margin-bottom: 20px;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        .input-container input[type="text"],
        .input-container input[type="datetime-local"] {
            width: calc(50% - 5px);
            padding: 10px;
            margin: 5px;
            box-sizing: border-box;
            border: 1px solid #221c1c;
            border-radius: 4px;
            font-size: 16px;
            text-emphasis-color: #000000;
        }

        .input-container button {
            width: 100px;
            background-color: #82b3e7;
            color: white;
            border: none;
            padding: 10px;
            cursor: pointer;
            border-radius: 4px;
            font-size: 16px;
            transition: background-color 0.3s ease;
        }

        .input-container button:hover {
            background-color: #0056b3;
        }

        .lists-container {
            display: flex;
            justify-content: space-between;
            flex-wrap: wrap;
        }

        .task-list, .completed-list {
            flex: 1 1 45%;
            margin-right: 10px;
            margin-bottom: 20px;
        }

        .list {
            list-style-type: none;
            padding: 0;
            text-align: left;
            background-color: #f0f0f0;
            border-radius: 8px;
            padding: 10px;
            height: 300px;
            overflow-y: auto;
        }

        .list li {
            margin-bottom: 10px;
            padding: 10px;
            background-color: #cac35f;
            border-radius: 4px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .list li.completed {
            background-color: #c3e6cb;
            text-decoration: line-through;
        }

        .list li span {
            flex: 1;
        }

        .list li .date-time {
            font-size: 12px;
            color: #0f0e0e;
        }

        .list li button {
            background-color: #dc3545;
            color: white;
            border: none;
            padding: 6px 12px;
            cursor: pointer;
            border-radius: 4px;
            font-size: 14px;
            transition: background-color 0.3s ease;
        }

        .list li button:hover {
            background-color: #c82333;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>To-Do List </h1>
        <div class="input-container">
            <input type="text" id="taskInput" placeholder="Enter task...">
            <input type="datetime-local" id="startTimeInput">
            <input type="datetime-local" id="endTimeInput">
            <button id="addTaskBtn">Add Task</button>
        </div>
        <div class="lists-container">
            <div class="task-list">
                <h2>Tasks</h2>
                <ul id="taskList" class="list"></ul>
            </div>
            <div class="completed-list">
                <h2>Completed Tasks</h2>
                <ul id="completedList" class="list"></ul>
            </div>
        </div>
     
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            const taskInput = document.getElementById('taskInput');
            const startTimeInput = document.getElementById('startTimeInput');
            const endTimeInput = document.getElementById('endTimeInput');
            const addTaskBtn = document.getElementById('addTaskBtn');
            const taskList = document.getElementById('taskList');
            const completedList = document.getElementById('completedList');
            let tasks = [];
            let completedTasks = [];

            addTaskBtn.addEventListener('click', addTask);

            function addTask() {
                const taskText = taskInput.value.trim();
                const startTime = new Date(startTimeInput.value);
                const endTime = new Date(endTimeInput.value);
                
                if (taskText !== '' && startTime && endTime) {
                    const task = {
                        id: Date.now(),
                        text: taskText,
                        startTime: startTime,
                        endTime: endTime
                    };
                    tasks.push(task);
                    displayTasks();
                    scheduleNotification(task);
                    resetInputs();
                }
            }

            function displayTasks() {
                taskList.innerHTML = '';
                tasks.forEach(task => {
                    const li = createTaskElement(task, 'tasks');
                    taskList.appendChild(li);
                });
            }

            function displayCompletedTasks() {
                completedList.innerHTML = '';
                completedTasks.forEach(task => {
                    const li = createTaskElement(task, 'completedTasks');
                    completedList.appendChild(li);
                });
            }

            function createTaskElement(task, listType) {
                const li = document.createElement('li');
                const createdFormatted = `${task.startTime.toLocaleDateString()} ${task.startTime.toLocaleTimeString()}`;
                const completedFormatted = task.completedAt ? `${task.completedAt.toLocaleDateString()} ${task.completedAt.toLocaleTimeString()}` : 'Not completed yet';
                li.innerHTML = `
                    <span>${task.text}</span>
                    <span class="date-time">Start Time: ${createdFormatted}, End Time: ${task.endTime.toLocaleDateString()} ${task.endTime.toLocaleTimeString()}</span>
                    <button class="delete-button">Delete</button>
                `;
                li.classList.add(listType === 'completedTasks' ? 'completed' : 'pending');
                if (listType === 'tasks') {
                    const deleteButton = li.querySelector('.delete-button');
                    deleteButton.addEventListener('click', () => deleteTask(task.id, 'tasks'));
                    const completeButton = document.createElement('button');
                    completeButton.textContent = 'Complete';
                    completeButton.className = 'complete-button';
                    completeButton.addEventListener('click', () => completeTask(task.id));
                    li.appendChild(completeButton);
                } else if (listType === 'completedTasks') {
                    const deleteButton = li.querySelector('.delete-button');
                    deleteButton.addEventListener('click', () => deleteTask(task.id, 'completedTasks'));
                }
                return li;
            }

            function completeTask(id) {
                const task = tasks.find(task => task.id === id);
                if (task) {
                    task.completedAt = new Date();
                    tasks = tasks.filter(task => task.id !== id);
                    completedTasks.push(task);
                    displayTasks();
                    displayCompletedTasks();
                }
            }

            function deleteTask(id, listType) {
                if (listType === 'tasks') {
                    tasks = tasks.filter(task => task.id !== id);
                    displayTasks();
                } else if (listType === 'completedTasks') {
                    completedTasks = completedTasks.filter(task => task.id !== id);
                    displayCompletedTasks();
                }
            }

            function scheduleNotification(task) {
                const currentTime = new Date();
                const timeDiff = task.endTime.getTime() - currentTime.getTime();
                
                if (timeDiff > 0) {
                    setTimeout(() => {
                        sendNotification(task);
                    }, timeDiff);
                }
            }

            function sendNotification(task) {
                // Example: Send email notification (backend function will handle this)
                fetch('/notify-task', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({ task })
                })
                .then(response => response.json())
                .then(data => console.log(data))
                .catch(error => console.error('Error sending notification:', error));
            }

            function resetInputs() {
                taskInput.value = '';
                startTimeInput.value = '';
                endTimeInput.value = '';
            }
        });
    </script>
</body>
</html>
