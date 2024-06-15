# task-manager
#include <iostream>
#include <string>
#include <limits>
#include <iomanip>
#include <vector>
#include <algorithm>

using namespace std;

// Define a Task structure
struct Task {
    int id;
    string description;
    int priority; 
    string dueDate;
    Task* next;

    Task(int taskId, const string& taskDescription, int taskPriority, const string& taskDueDate)
        : id(taskId), description(taskDescription), priority(taskPriority), dueDate(taskDueDate), next(nullptr) {}
};

// Define the TaskManager class
class TaskManager {
private:
    Task* head;
    int nextId;

public:
    // Constructor
    TaskManager() : head(nullptr), nextId(1) {}

    // Destructor
    ~TaskManager() {
        clearTasks();
    }

    // Copy constructor
    TaskManager(const TaskManager& other) : head(nullptr), nextId(1) {
        Task* temp = other.head;
        while (temp != nullptr) {
            addTask(temp->description, temp->priority, temp->dueDate);
            temp = temp->next;
        }
    }

    // Assignment operator
    TaskManager& operator=(const TaskManager& other) {
        if (this == &other) {
            return *this;
        }

        clearTasks();

        Task* temp = other.head;
        while (temp != nullptr) {
            addTask(temp->description, temp->priority, temp->dueDate);
            temp = temp->next;
        }

        return *this;
    }

    // Function to add a task
    void addTask(const string& description, int priority, const string& dueDate) {
        Task* newTask = new Task(nextId++, description, priority, dueDate);

        if (head == nullptr) {
            head = newTask;
        } else {
            Task* temp = head;
            while (temp->next != nullptr) {
                temp = temp->next;
            }
            temp->next = newTask;
        }

        cout << "Task added successfully!\n";
    }

    // Function to delete a task
    void deleteTask(int id) {
        if (head == nullptr) {
            cout << "No tasks to delete.\n";
            return;
        }

        if (head->id == id) {
            Task* temp = head;
            head = head->next;
            delete temp;
            cout << "Task deleted successfully!\n";
            return;
        }

        Task* temp = head;
        while (temp->next != nullptr && temp->next->id != id) {
            temp = temp->next;
        }

        if (temp->next == nullptr) {
            cout << "Task with ID " << id << " not found.\n";
        } else {
            Task* toDelete = temp->next;
            temp->next = temp->next->next;
            delete toDelete;
            cout << "Task deleted successfully!\n";
        }
    }

    // Function to update a task
    void updateTask(int id, const string& newDescription, int newPriority, const string& newDueDate) {
        Task* temp = head;
        while (temp != nullptr) {
            if (temp->id == id) {
                temp->description = newDescription;
                temp->priority = newPriority;
                temp->dueDate = newDueDate;
                cout << "Task updated successfully!\n";
                return;
            }
            temp = temp->next;
        }
        cout << "Task with ID " << id << " not found.\n";
    }

    // Function to search tasks by description
    void searchTask(const string& description) {
        Task* temp = head;
        bool found = false;
        while (temp != nullptr) {
            if (temp->description.find(description) != string::npos) {
                cout << "Task ID: " << temp->id << ", Description: " << temp->description << ", Priority: "
                     << temp->priority << ", Due Date: " << temp->dueDate << "\n";
                found = true;
            }
            temp = temp->next;
        }
        if (!found) {
            cout << "No tasks matching the description found.\n";
        }
    }

    // Function to view all tasks
    void viewTasks() const {
        if (head == nullptr) {
            cout << "No tasks available.\n";
            return;
        }

        Task* temp = head;
        while (temp != nullptr) {
            cout << "Task ID: " << temp->id << ", Description: " << temp->description << ", Priority: "
                 << temp->priority << ", Due Date: " << temp->dueDate << "\n";
            temp = temp->next;
        }
    }

    // Function to count the number of tasks
    void countTasks() const {
        int count = 0;
        Task* temp = head;
        while (temp != nullptr) {
            count++;
            temp = temp->next;
        }
        cout << "Total number of tasks: " << count << "\n";
    }

    // Function to clear all tasks
    void clearTasks() {
        while (head != nullptr) {
            Task* temp = head;
            head = head->next;
            delete temp;
        }
    }

    // Function to sort tasks by priority (ascending)
    void sortTasksByPriority() {
        vector<Task*> tasks;
        Task* temp = head;
        while (temp != nullptr) {
            tasks.push_back(temp);
            temp = temp->next;
        }

        sort(tasks.begin(), tasks.end(), [](Task* a, Task* b) {
            return a->priority < b->priority;
        });

        head = nullptr;
        for (Task* task : tasks) {
            task->next = nullptr;
            if (head == nullptr) {
                head = task;
            } else {
                Task* last = head;
                while (last->next != nullptr) {
                    last = last->next;
                }
                last->next = task;
            }
        }

        cout << "Tasks sorted by priority.\n";
    }
};

// Utility function to clear input buffer
void clearInputBuffer() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

// Main function
int main() {
    TaskManager tm;
    int choice;
    string description;
    int id, priority;
    string dueDate;

    while (true) {
        cout << "\nTask Management System\n";
        cout << "1. Add Task\n";
        cout << "2. Delete Task\n";
        cout << "3. Update Task\n";
        cout << "4. Search Task\n";
        cout << "5. View Tasks\n";
        cout << "6. Count Tasks\n";
        cout << "7. Sort Tasks by Priority\n";
        cout << "8. Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;

        if (cin.fail()) {
            cout << "Invalid input. Please enter a number.\n";
            clearInputBuffer();
            continue;
        }

        switch (choice) {
        case 1:
            cout << "Enter task description: ";
            clearInputBuffer();
            getline(cin, description);
            cout << "Enter task priority (1 highest - 5 lowest): ";
            cin >> priority;
            if (cin.fail() || priority < 1 || priority > 5) {
                cout << "Invalid priority. Please enter a number between 1 and 5.\n";
                clearInputBuffer();
            } else {
                cout << "Enter due date (optional): ";
                clearInputBuffer();
                getline(cin, dueDate);
                tm.addTask(description, priority, dueDate);
            }
            break;
        case 2:
            cout << "Enter task ID to delete: ";
            cin >> id;
            if (cin.fail()) {
                cout << "Invalid ID. Please enter a valid number.\n";
                clearInputBuffer();
            } else {
                tm.deleteTask(id);
            }
            break;
        case 3:
            cout << "Enter task ID to update: ";
            cin >> id;
            if (cin.fail()) {
                cout << "Invalid ID. Please enter a valid number.\n";
                clearInputBuffer();
            } else {
                cout << "Enter new task description: ";
                clearInputBuffer();
                getline(cin, description);
                cout << "Enter new task priority (1 highest - 5 lowest): ";
                cin >> priority;
                if (cin.fail() || priority < 1 || priority > 5) {
                    cout << "Invalid priority. Please enter a number between 1 and 5.\n";
                    clearInputBuffer();
                } else {
                    cout << "Enter new due date (optional): ";
                    clearInputBuffer();
                    getline(cin, dueDate);
                    tm.updateTask(id, description, priority, dueDate);
                }
            }
            break;
        case 4:
            cout << "Enter description to search: ";
            clearInputBuffer();
            getline(cin, description);
            tm.searchTask(description);
            break;
        case 5:
            tm.viewTasks();
            break;
        case 6:
            tm.countTasks();
            break;
        case 7:
            tm.sortTasksByPriority();
            break;
        case 8:
            cout << "Exiting the system. Goodbye!\n";
            return 0;
        default:
            cout << "Invalid choice. Please try again.\n";
        }
    }

    return 0;
}
