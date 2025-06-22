# SQL

## Створення таблиць

```sql
CREATE TABLE users (
    user_id SERIAL PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password_hash TEXT NOT NULL,
    role VARCHAR(20) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    is_active BOOLEAN DEFAULT TRUE
);

CREATE TABLE projects (
    project_id SERIAL PRIMARY KEY,
    title VARCHAR(150) NOT NULL,
    description TEXT,
    created_at DATE NOT NULL,
    deadline DATE,
    status VARCHAR(30) DEFAULT 'active',
    owner_id INT REFERENCES users(user_id)
);

CREATE TABLE tasks (
    task_id SERIAL PRIMARY KEY,
    title VARCHAR(200) NOT NULL,
    description TEXT,
    status VARCHAR(30) DEFAULT 'new',
    priority VARCHAR(20) DEFAULT 'medium',
    deadline DATE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP,
    project_id INT REFERENCES projects(project_id),
    assignee_id INT REFERENCES users(user_id)
);

CREATE TABLE comments (
    comment_id SERIAL PRIMARY KEY,
    content TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    task_id INT REFERENCES tasks(task_id),
    author_id INT REFERENCES users(user_id)
);

CREATE TABLE attachments (
    file_id SERIAL PRIMARY KEY,
    file_name VARCHAR(255),
    file_type VARCHAR(50),
    file_url TEXT,
    uploaded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    task_id INT REFERENCES tasks(task_id)
);
```

## Вставка даних

```sql
INSERT INTO users (first_name, last_name, email, password_hash, role)
VALUES
('Іван', 'Петренко', 'ivan@pmtool.com', 'hash1', 'manager'),
('Олена', 'Коваленко', 'olena@pmtool.com', 'hash2', 'executor'),
('Марко', 'Семенов', 'mark@pmtool.com', 'hash3', 'executor');

INSERT INTO projects (title, description, created_at, deadline, owner_id)
VALUES
('Система керування задачами', 'Веб-сервіс для управління ІТ-проєктами', '2025-06-01', '2025-08-31', 1);

INSERT INTO tasks (title, description, project_id, assignee_id, priority, deadline)
VALUES
('Створення ER-моделі', 'Розробити сутнісно-зв’язну модель даних', 1, 2, 'high', '2025-06-10'),
('Реалізація REST API', 'Створити базовий набір endpoint-ів', 1, 3, 'medium', '2025-06-15');
```

## Базові SELECT-запити

```sql
-- Усі активні користувачі
SELECT * FROM users WHERE is_active = TRUE;

-- Усі задачі проєкту з ID 1
SELECT title, status, deadline
FROM tasks
WHERE project_id = 1;
```

## JOIN-запит

```sql
-- Задачі з прізвищем виконавця
SELECT tasks.title, tasks.status, users.last_name
FROM tasks
JOIN users ON tasks.assignee_id = users.user_id;
```
## Оновлення та видалення

```sql
-- Оновити статус задачі на "done"
UPDATE tasks
SET status = 'done', updated_at = CURRENT_TIMESTAMP
WHERE task_id = 1;

-- Видалити користувача за email
DELETE FROM users
WHERE email = 'olena@pmtool.com';
```

## Агрегація

```sql
-- Кількість задач у кожному проєкті
SELECT projects.title, COUNT(tasks.task_id) AS total_tasks
FROM projects
LEFT JOIN tasks ON projects.project_id = tasks.project_id
GROUP BY projects.title;
```

## Висновок

Розроблені SQL-запити реалізують повноцінну базу даних для інформаційної системи керування проєктами. Вони охоплюють створення таблиць, вставку даних, базові та складні вибірки, оновлення та видалення даних.
