<h1 id="library-management-system">Library Management System with Secure Token Authentication</h1>

<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#library-management-system">About The Project</a>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#installing">Installing</a></li>
      </ul>
    </li>
    <li><a href="#usage">Usage</a>
        <ul>
        <li><a href="#user-endpoints">User Endpoints</a></li>
        <li><a href="#author-endpoints">Author Endpoints</a></li>
        <li><a href="#book-endpoints">Book Endpoints</a></li>
        <li><a href="#book-author-association-endpoints">Book-Author Association Endpoints</a></li>
      </ul>
    </li>
    <li><a href="#token-management">Token Management</a></li>
    <li><a href="#troubleshooting--faq">Troubleshooting / FAQ</a></li>
    <li><a href="#project-information">Project Information</a></li>
  </ol>
</details>

## About the Project

The Library Management System offers a reliable and streamlined solution for managing books, authors, users, and their associations. It supports comprehensive CRUD functionality for users (including registration, authentication, retrieval, updates, and deletion), as well as for books, authors, and their relationships. Secure access is ensured through token-based authentication, which includes validation and usage tracking to limit actions to authorized users. A dedicated book-author relationship table provides enhanced flexibility by effectively linking books with their authors. The system is designed to simplify library data management while prioritizing robust security measures.

<p align="right">(<a href="#library-management-system">back to top</a>)</p>

## Getting Started

### Prerequisites

- XAMPP
- SQLyog (optional, can use phpMyAdmin)
- Composer
- Node.js
- PHP (version 7.2 or higher)
- Slim Framework
- JWT PHP Library
- ThunderClient

### Installing

1. **Clone the Repository**

   ```bash
   git clone https://github.com/github_username/library_4a.git
   cd /path/to/xampp/htdocs/library_4a

   ```

2. **Install Dependencies**

   - Use Composer to install PHP dependencies:

   ```bash
   composer install

   ```

3. **Set Up Database**

   - Open SQLyog or phpMyAdmin and create a new database called `library`.
   - Run the following SQL queries to create the required tables:

   ```sql
   CREATE TABLE users (
       userid INT(9) NOT NULL AUTO_INCREMENT,
       username CHAR(255) NOT NULL,
       password TEXT NOT NULL,
       PRIMARY KEY (userid)
   );

   CREATE TABLE authors (
       authorid INT(9) NOT NULL AUTO_INCREMENT,
       name CHAR(255) NOT NULL,
       PRIMARY KEY (authorid)
   );

   CREATE TABLE books (
       bookid INT(9) NOT NULL AUTO_INCREMENT,
       title CHAR(255) NOT NULL,
       PRIMARY KEY (bookid)
   );

   CREATE TABLE books_authors (
       collectionid INT(9) NOT NULL AUTO_INCREMENT,
       bookid INT(9) NOT NULL,
       authorid INT(9) NOT NULL,
       PRIMARY KEY (collectionid)
   );

   CREATE TABLE used_tokens (
       token VARCHAR(512) PRIMARY KEY,
       used_at DATETIME NOT NULL
   );
   ```

4. **Configure Database Connection**

   - Edit the connection details in index.php as follows:

   ```php
   <?php
   $servername = "localhost";
   $username = "root";
   $password = "password";
   $dbname = "library";
   ?>
   ```

   Replace these values with your actual database settings to connect to the library database.

5. **Start XAMPP Server**

   - Ensure that both Apache and MySQL are running in the XAMPP control panel.

6. **Testing the Application**
   - You can now use API testing tools like Postman or Thunder Client to test the CRUD operations and authentication endpoints.

<p align="right">(<a href="#library-management-system">back to top</a>)</p>

## Usage

<h3 id="user-endpoints">1. User Endpoints</h3>

**a. User Registration** - Registers a new user with a unique username and a hashed password.

- **Endpoint:** `/user/register`
- **Method:** `POST`
- **Sample Payload:**

  ```json
  {
    "username": "uniqueUser",
    "password": "uniquePassword"
  }
  ```

- **Expected Response:**

  - **Success:**

    ```json
    {
      "status": "success",
      "data": null
    }
    ```

  - **Failure:**

    ```json
    {
      "status": "fail",
      "data": {
        "title": "<Error Message Here>"
      }
    }
    ```

**b. User Authentication** - Authenticates a user and generates a JWT token.

- **Endpoint:** `/user/authenticate`
- **Method:** `POST`
- **Sample Payload:**

  ```json
  {
    "username": "existingUser",
    "password": "uniquePassword"
  }
  ```

- **Expected Response:**

  - **Success:**

    ```json
    {
      "status": "success",
      "token": "jwtTokenHere",
      "data": null
    }
    ```

  - **Failure:**

    ```json
    {
      "status": "fail",
      "data": {
        "title": "Authentication Failed"
      }
    }
    ```

**c. Display Users** - Retrieves a list of all users in the system; requires a valid token.

- **Endpoint:** `/user/display`
- **Method:** `GET`
- **Headers:** `Authorization: Bearer <insert generated jwtTokenHere>`

- **Expected Response:**

  - **Success:**

    ```json
    {
      "status": "success",
      "token": "jwtTokenHere",
      "data": [
        {
          "userid": 1,
          "username": "username"
        }
      ]
    }
    ```

  - **Failure:** Token Already Used

    ```json
    {
      "status": "fail",
      "data": {
        "title": "Token has already been used"
      }
    }
    ```

  - **Failure:** Invalid or Expired Token

    ```json
    {
      "status": "fail",
      "data": {
        "title": "Invalid or expired token"
      }
    }
    ```

**d. Update User Information** - Updates the user's username and/or password; requires a valid token.

- **Endpoint:** `/user/update`
- **Method:** `PUT`
- **Headers:** `Authorization: Bearer <insert generated jwtTokenHere>`
- **Sample Payload:**

  ```json
  {
    "username": "updatedUser",
    "password": "newUniquePassword"
  }
  ```

- **Expected Response:**

  - **Success:**

    ```json
    {
      "status": "success",
      "token": "jwtTokenHere",
      "data": null
    }
    ```

  - **Failure:** If the new username is taken, if there’s nothing to update, or if the token is invalid, expired, or already used, an appropriate error message.

**e. Delete User** - Deletes the authenticated user’s account from the system; requires a valid token.

- **Endpoint:** `/user/delete`
- **Method:** `DELETE`
- **Headers:** `Authorization: Bearer <insert generated jwtTokenHere>`

- **Expected Response:**

  - **Success:**

    ```json
    {
      "status": "success",
      "data": null
    }
    ```

  - **Failure:** If the user doesn’t exist, or if the token is invalid, expired, or already used, an appropriate error message.

<p align="right">(<a href="#library-management-system">back to top</a>)</p>

<h3 id="author-endpoints">2. Author Endpoints</h3>

**a. Add Author** - Adds a new author to the database.

- **Endpoint:** `/author/add`
- **Method:** `POST`
- **Headers:** `Authorization: Bearer <insert generated jwtTokenHere>`
- **Sample Payload:**

  ```json
  {
    "name": "Author Name"
  }
  ```

- **Expected Response:**

  - **Success:**

    ```json
    {
      "status": "success",
      "token": "jwtTokenHere",
      "data": null
    }
    ```

  - **Failure:** If the token is invalid, expired, already used, or if the name is empty or the author already exists, an appropriate error message will be returned.

**b. Display Author** - Displays a list of authors from the database.

- **Endpoint:** `/author/display`
- **Method:** `GET`
- **Headers:** `Authorization: Bearer <insert generated jwtTokenHere>`

- **Expected Response:**

  - **Success:**

    ```json
    {
      "status": "success",
      "token": "jwtTokenHere",
      "data": [
        {
          "authorid": 1,
          "name": "Author Name"
        }
      ]
    }
    ```

  - **Failure:** If the token has already been used, is invalid, or has expired, an appropriate error message will be returned.

**c. Update Author** - Updates an author's information in the database.

- **Endpoint:** `/author/update`
- **Method:** `PUT`
- **Headers:** `Authorization: Bearer <insert generated jwtTokenHere>`
- **Sample Payload:**

  ```json
  {
    "authorid": 1,
    "name": "Updated Author Name"
  }
  ```

- **Expected Response:**

  - **Success:**

    ```json
    {
      "status": "success",
      "token": "jwtTokenHere",
      "data": null
    }
    ```

  - **Failure:** If the token has already been used, is invalid, expired, or if the author ID is missing or not found, or if there are no fields to update, an appropriate error message will be returned.

**d. Delete Author** - Deletes an author from the database.

- **Endpoint:** `/author/delete`
- **Method:** `DELETE`
- **Headers:** `Authorization: Bearer <insert generated jwtTokenHere>`
- **Sample Payload:**

  ```json
  {
    "authorid": 1
  }
  ```

- **Expected Response:**

  - **Success:**

    ```json
    {
      "status": "success",
      "token": "jwtTokenHere",
      "data": null
    }
    ```

  - **Failure:** If the token has already been used, is invalid, expired, or if the author ID is missing or not found, an appropriate error message will be returned.

<p align="right">(<a href="#library-management-system">back to top</a>)</p>

<h3 id="book-endpoints">3. Book Endpoints</h3>

**a. Add Book** - Adds a new book to the database.

- **Endpoint:** `/book/add`
- **Method:** `POST`
- **Headers:** `Authorization: Bearer <insert generated jwtTokenHere>`
- **Sample Payload:**

  ```json
  {
    "title": "Book Title"
  }
  ```

- **Expected Response:**

  - **Success:**

    ```json
    {
      "status": "success",
      "token": "jwtTokenHere",
      "data": null
    }
    ```

  - **Failure:** If the token is invalid, expired, already used, or if the title is empty or the book already exists, an appropriate error message will be returned.

**b. Display Books** - Displays a list of books from the database.

- **Endpoint:** `/book/display`
- **Method:** `GET`
- **Headers:** `Authorization: Bearer <insert generated jwtTokenHere>`

- **Expected Response:**

  - **Success:**

    ```json
    {
      "status": "success",
      "token": "jwtTokenHere",
      "data": [
        {
          "bookid": 1,
          "title": "Book Title"
        }
      ]
    }
    ```

  - **Failure:** If the token has already been used, is invalid, or expired, an appropriate error message will be returned.

**c. Update Book** - Updates a book's information in the database.

- **Endpoint:** `/book/update`
- **Method:** `PUT`
- **Headers:** `Authorization: Bearer <insert generated jwtTokenHere>`
- **Sample Payload:**

  ```json
  {
    "bookid": 1,
    "title": "Updated Book Title"
  }
  ```

- **Expected Response:**

  - **Success:**

    ```json
    {
      "status": "success",
      "token": "jwtTokenHere",
      "data": null
    }
    ```

  - **Failure:** If the token has already been used, is invalid, expired, or if the book ID is missing or not found, or if there are no fields to update, an appropriate error message will be returned.

**d. Delete Book** - Deletes a book from the database.

- **Endpoint:** `/book/delete`
- **Method:** `DELETE`
- **Headers:** `Authorization: Bearer <insert generated jwtTokenHere>`
- **Sample Payload:**

  ```json
  {
    "bookid": 1
  }
  ```

- **Expected Response:**

  - **Success:**

    ```json
    {
      "status": "success",
      "token": "jwtTokenHere",
      "data": null
    }
    ```

  - **Failure:** If the token has already been used, is invalid, expired, or if the book ID is missing or not found, an appropriate error message will be returned.

<p align="right">(<a href="#library-management-system">back to top</a>)</p>

## Token Management

**Token Rotation**

The `generateToken` function creates a JWT for user authentication, with a 2-hour expiration time, including the user ID in the payload, and the token is signed using the HS256 algorithm.

```php
function generateToken($userid)
{
    global $key;

    $iat = time();
    $exp = $iat + 7200;

    $payload = [
        'iss' => 'http://library.org',
        'aud' => 'http://library.com',
        'iat' => $iat,
        'exp' => $exp,
        'data' => [
            'userid' => $userid
        ]
    ];

    return JWT::encode($payload, $key, 'HS256');
}
```

**Check if Token is Used**  
The `isTokenUsed` function checks the `used_tokens` table to see if the token has been recorded as used.

```php
function isTokenUsed($token, $conn)
{
    $stmt = $conn->prepare("SELECT * FROM used_tokens WHERE token = :token");
    $stmt->bindParam(':token', $token);
    $stmt->execute();
    return $stmt->rowCount() > 0;
}
```

**Validate Token**  
The `validateToken` function decodes and validates the token using the secret key, returning `false` if the token is invalid or expired.

```php
function validateToken($token, $key)
{
    try {
        return JWT::decode($token, new Key($key, 'HS256'));
    } catch (Exception $e) {
        return false;
    }
}
```

**Mark Token as Used**  
The `markTokenAsUsed` function inserts the token into the `used_tokens` table, marking it as used to prevent reuse.

```php
function markTokenAsUsed($conn, $token)
{
    try {
        $stmt = $conn->prepare("INSERT INTO used_tokens (token) VALUES (:token)");
        $stmt->bindParam(':token', $token);
        $stmt->execute();
    } catch (PDOException $e) {
        throw new Exception("Error marking token as used: " . $e->getMessage());
    }
}
```

<p align="right">(<a href="#library-management-system">back to top</a>)</p>

## Project Information

This project was developed as a midterm requirement for the ITPC 115 course. It showcases proficiency in designing secure API endpoints and implementing effective token management.

<p align="right">(<a href="#library-management-system">back to top</a>)</p>

## Contact Information

If you need assistance or have any questions, feel free to reach out to me. Below are my contact details:

- **Name:** Kyle D. De Jesus
- **University:** Don Mariano Marcos Memorial State University (Mid-La Union Campus)
- **Email:** kdejesus16692@student.dmmmsu.edu.ph
- **Phone:** 09103046087
<p align="right">(<a href="#library-management-system">back to top</a>)</p>
