Here's a complete example to create a simple HTML form that captures data (company name, address, employee name, and technology stack) and stores it in a database. The backend will be in Node.js with Express and use PostgreSQL as the database.

### Project Structure:
```plaintext
project/
├── server.js         // Node.js server file
├── public/           
│   └── index.html    // HTML form page
└── db.js             // Database connection file
```

### Step 1: Set up the PostgreSQL database
1. Create a PostgreSQL database, e.g., `companydb`.
2. Create a table to store the form data:

```sql
CREATE TABLE employees (
    id SERIAL PRIMARY KEY,
    company_name VARCHAR(255),
    address VARCHAR(255),
    employee_name VARCHAR(255),
    technology_stack VARCHAR(255)
);
```

### Step 2: Install Node.js and Required Packages
In the project directory, initialize a Node.js project and install the necessary packages:
```bash
npm init -y
npm install express body-parser pg
```

### Step 3: Database Connection (`db.js`)
Create a `db.js` file to set up the PostgreSQL connection.

```javascript
// db.js
const { Pool } = require('pg');

const pool = new Pool({
    user: 'your_username',         // replace with your PostgreSQL username
    host: 'localhost',              // replace if different
    database: 'companydb',          // replace with your database name
    password: 'your_password',      // replace with your PostgreSQL password
    port: 5432,                     // default PostgreSQL port
});

module.exports = pool;
```

### Step 4: HTML Form (`public/index.html`)
Create the `index.html` file to collect user input.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Employee Form</title>
</head>
<body>
    <h1>Employee Information Form</h1>
    <form id="employeeForm" action="/submit" method="POST">
        <label for="companyName">Company Name:</label>
        <input type="text" id="companyName" name="companyName" required><br><br>

        <label for="address">Address:</label>
        <input type="text" id="address" name="address" required><br><br>

        <label for="employeeName">Employee Name:</label>
        <input type="text" id="employeeName" name="employeeName" required><br><br>

        <label for="technologyStack">Technology Stack:</label>
        <input type="text" id="technologyStack" name="technologyStack" required><br><br>

        <button type="submit">Submit</button>
    </form>
</body>
</html>
```

### Step 5: Express Server (`server.js`)
Create the `server.js` file to handle form submissions and store data in PostgreSQL.

```javascript
// server.js
const express = require('express');
const bodyParser = require('body-parser');
const pool = require('./db');
const path = require('path');

const app = express();
const PORT = 3000;

// Middleware
app.use(bodyParser.urlencoded({ extended: true }));
app.use(express.static(path.join(__dirname, 'public')));

// Route to handle form submission
app.post('/submit', async (req, res) => {
    const { companyName, address, employeeName, technologyStack } = req.body;

    try {
        const query = `
            INSERT INTO employees (company_name, address, employee_name, technology_stack)
            VALUES ($1, $2, $3, $4) RETURNING *`;
        const values = [companyName, address, employeeName, technologyStack];
        
        const result = await pool.query(query, values);
        console.log('Data inserted:', result.rows[0]);

        res.send('<h2>Form submitted successfully!</h2><a href="/">Go Back</a>');
    } catch (err) {
        console.error('Error inserting data:', err);
        res.status(500).send('Error inserting data');
    }
});

// Start server
app.listen(PORT, () => {
    console.log(`Server is running on http://localhost:${PORT}`);
});
```

### Step 6: Run the Server
Start the server by running:
```bash
node server.js
```

### How it Works:
1. Navigate to `http://localhost:3000` in your browser to see the HTML form.
2. Fill in the form and click **Submit**.
3. The form data is sent to the `/submit` endpoint on the server, which stores the data in the PostgreSQL database.
4. After submission, you'll see a success message.

This basic setup provides a complete front-to-back solution for capturing form data in HTML and storing it in a PostgreSQL database using Node.js.
