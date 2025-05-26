# TodoListManager
# 📝 To-Do List Manager – Smart Contract (Solidity)

A decentralized **To-Do List Manager** written in Solidity, allowing users to **create**, **complete**, and **delete** their own **on-chain tasks**. Each user's tasks are securely managed and scoped to their wallet address, ensuring privacy and autonomy over their task list.

---

## 🚀 How It Works

This smart contract allows users to interact directly with the Ethereum blockchain (or compatible EVM networks) to manage personal tasks. Each wallet address acts as a separate user, with their tasks stored in the blockchain's storage.

### ⚙️ Core Functionality

- Users create new tasks by providing task descriptions.
- Each task is assigned a **unique task ID** and stored in a per-user list.
- Tasks can be:
  - **Completed** — marked as done
  - **Deleted** — removed from the task list
- All tasks are fully **on-chain** and **user-specific**.

---

## ✨ Features

| Feature          | Description                                                              |
|------------------|--------------------------------------------------------------------------|
| 🆕 `createTask()`   | Creates a new task for the sender with a unique ID and content         |
| 📋 `getMyTasks()`   | Retrieves the entire list of tasks created by the user                |
| ✅ `completeTask()` | Marks a specific task (by ID) as completed                             |
| ❌ `deleteTask()`   | Deletes a task from the sender's list by ID                            |
| 🔐 User Isolation  | Each user can **only** access and manage **their own tasks**            |
| 🧾 Event Logs       | Emits events for `TaskCreated`, `TaskCompleted`, and `TaskDeleted`      |

---
Contract Details:0x8d59a2d86ccb63a81e22c60901ec174cf98573b1aa1e3127fa3231347c862d92
![image](https://github.com/user-attachments/assets/259aa002-4a01-4492-b277-d164e3fa0575)

## 📄 Smart Contract Code

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract TodoListManager {
    
    uint256 private taskIdCounter;

    struct Task {
        uint256 id;
        string content;
        bool completed;
    }

    // Mapping from user address to their task list
    mapping(address => Task[]) private userTasks;

    // Events
    event TaskCreated(address indexed user, uint256 taskId, string content);
    event TaskCompleted(address indexed user, uint256 taskId);
    event TaskDeleted(address indexed user, uint256 taskId);

    // Create a new task
    function createTask(string calldata _content) external {
        Task memory newTask = Task({
            id: taskIdCounter,
            content: _content,
            completed: false
        });

        userTasks[msg.sender].push(newTask);
        emit TaskCreated(msg.sender, taskIdCounter, _content);

        taskIdCounter++;
    }

    // View all tasks of sender
    function getMyTasks() external view returns (Task[] memory) {
        return userTasks[msg.sender];
    }

    // Mark a task as completed
    function completeTask(uint256 _taskId) external {
        Task[] storage tasks = userTasks[msg.sender];
        for (uint256 i = 0; i < tasks.length; i++) {
            if (tasks[i].id == _taskId) {
                tasks[i].completed = true;
                emit TaskCompleted(msg.sender, _taskId);
                return;
            }
        }
        revert("Task not found");
    }

    // Delete a task
    function deleteTask(uint256 _taskId) external {
        Task[] storage tasks = userTasks[msg.sender];
        for (uint256 i = 0; i < tasks.length; i++) {
            if (tasks[i].id == _taskId) {
                // Move the last task to the deleted spot to keep array compact
                tasks[i] = tasks[tasks.length - 1];
                tasks.pop();
                emit TaskDeleted(msg.sender, _taskId);
                return;
            }
        }
        revert("Task not found");
    }
}

