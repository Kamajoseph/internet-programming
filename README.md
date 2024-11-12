 task one

setting up express server

const express = require('express');
const mysql = require('mysql2');
const bodyParser = require('body-parser');
const path = require('path');
require('dotenv').config();

const app = express();
const port = 3000;

// Database connection
const db = mysql.createConnection({
    host: process.env.DB_HOST,
    user: process.env.DB_USER,
    password: process.env.DB_PASSWORD,
    database: process.env.DB_NAME
});

db.connect((err) => {
    if (err) throw err;
    console.log('Connected to MySQL database');
});

// Middleware
app.use(bodyParser.urlencoded({ extended: true }));
app.set('view engine', 'ejs');
app.use(express.static(path.join(__dirname, 'public')));

app.listen(port, () => {
    console.log(`Server is running on http://localhost:${port}`);
});







Task 2

database creation

CREATE DATABASE obituary_platform;
USE obituary_platform;

CREATE TABLE obituaries (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    date_of_birth DATE,
    date_of_death DATE,
    content TEXT,
    author VARCHAR(100),
    submission_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    slug VARCHAR(255) UNIQUE
);

Task 3 HTML form creation

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Submit Obituary</title>
    <link rel="stylesheet" href="/css/styles.css">
</head>
<body>
    <h1>Submit Obituary</h1>
    <form action="/submit" method="POST" id="obituaryForm">
        <label for="name">Name</label>
        <input type="text" name="name" id="name" required>

        <label for="date_of_birth">Date of Birth</label>
        <input type="date" name="date_of_birth" id="date_of_birth" required>

        <label for="date_of_death">Date of Death</label>
        <input type="date" name="date_of_death" id="date_of_death" required>

        <label for="content">Content</label>
        <textarea name="content" id="content" required></textarea>

        <label for="author">Author</label>
        <input type="text" name="author" id="author" required>

        <button type="submit">Submit</button>
    </form>

    <script src="/js/validation.js"></script>
</body>
</html>

CSS form creation

body {
    font-family: Arial, sans-serif;
}
form {
    max-width: 600px;
    margin: auto;
    padding: 20px;
    border: 1px solid #ddd;
}
label {
    display: block;
    margin: 10px 0 5px;
}
input, textarea {
    width: 100%;
    padding: 8px;
    margin-bottom: 10px;
}
button {
    background-color: #4CAF50;
    color: white;
    padding: 10px;
    border: none;
    cursor: pointer;
}


javascript form creation

document.getElementById('obituaryForm').addEventListener('submit', function(e) {
    let form = this;
    if (!form.name.value || !form.date_of_birth.value || !form.date_of_death.value || !form.content.value || !form.author.value) {
        alert("All fields are required.");
        e.preventDefault();
    }
});

task 4 backend script for data submission

app.post('/submit', (req, res) => {
    const { name, date_of_birth, date_of_death, content, author } = req.body;
    const slug = name.toLowerCase().replace(/\s+/g, '-') + '-' + Date.now(); // Simple slug generation

    const query = `INSERT INTO obituaries (name, date_of_birth, date_of_death, content, author, slug) 
                   VALUES (?, ?, ?, ?, ?, ?)`;
    db.query(query, [name, date_of_birth, date_of_death, content, author, slug], (err, result) => {
        if (err) {
            console.error(err);
            return res.status(500).send('Error occurred during submission');
        }
        res.redirect('/obituaries');
    });
});

Create a route to show the form:

Add a GET route for the form page:
js
app.get('/submit', (req, res) => {
    res.render('obituary_form');
});


Task 5: Backend Script for Data Retrieval

Retrieve and display obituaries:

Create a route in server.js to fetch all obituaries from the database and display them

app.get('/obituaries', (req, res) => {
    db.query('SELECT * FROM obituaries', (err, results) => {
        if (err) throw err;
        res.render('view_obituaries', { obituaries: results });
    });
});


Create the view_obituaries.ejs template to display the obituaries in a table format:

<h1>View Obituaries</h1>
<table>
    <thead>
        <tr>
            <th>Name</th>
            <th>Date of Birth</th>
            <th>Date of Death</th>
            <th>Author</th>
            <th>Content</th>
        </tr>
    </thead>
    <tbody>
        <% obituaries.forEach(function(obituary) { %>
            <tr>
                <td><%= obituary.name %></td>
                <td><%= obituary.date_of_birth %></td>
                <td><%= obituary.date_of_death %></td>
                <td><%= obituary.author %></td>
                <td><%= obituary.content.substring(0, 100) %>...</td>
            </tr>
        <% }); %>
    </tbody>
</table>

Task 6: SEO and Social Media Optimization

1.Add SEO meta tags dynamically:

<meta name="description" content="<%= obituary.content.substring(0, 150) %>">
<meta property="og:title" content="<%= obituary.name %>">
<meta property="og:description" content="<%= obituary.content.substring(0, 150) %>">
<meta property="og:image" content="/images/obituary-image.jpg">



