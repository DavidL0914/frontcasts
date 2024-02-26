<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Edit Page</title>
    <link rel="stylesheet" href="frontcasts-styling.scss">
</head>
<body>
    <div class="container">
        <pre id="data"></pre>
        <form>
            <input type="text" id="username" class="input" placeholder = "New Username">
            <input type="text" id="uid" class="input" placeholder = "User ID">
        </form>
        <button class="submit" onclick="update()">Change Username</button>
        <form>
            <input type="text" id="_uid" class="input" placeholder = "User ID">
        </form>
        <button class="submit" onclick="del()">Delete User</button>
    </div>
    <script>
        // Apply theme preference from local storage
        const theme = localStorage.getItem('theme');
        if (theme) {
            document.body.classList.add(theme);
        }
        // Function to update username
        function update() {
            data = {
                "name": document.getElementById("username").value,
                "uid": document.getElementById("uid").value
            }
            let options = {
                method: 'PUT',
                headers: {
                    'Content-Type': 'application/json;charset=utf-8',
                },
                credentials: 'include',
                body: JSON.stringify(data)
            }
            fetch("{{site.backendurl}}/api/users/", options)
                .then(response => {
                    let access = response.status !== 401 && response.status !== 403;
                    return response.json().then(data => ({ data, access }));
                })
                .then(({ data, access }) => {
                    console.log(access)
                    if (access) {
                        document.getElementById("data").textContent = "Data Successfully Changed";
                    } else {
                        document.getElementById("data").textContent = "Unauthorized.";
                    }
                })
        }
        // Function to delete user
        function del() {
            data = {
                "uid": document.getElementById("_uid").value,
                "role": "admin"
            }
            let options = {
                method: 'DELETE',
                headers: {
                    'Content-Type': 'application/json;charset=utf-8',
                },
                credentials: 'include',
                body: JSON.stringify(data)
            }
            fetch("{{site.backendurl}}/api/users/", options)
                .then(response => {
                    let access = response.status !== 401 && response.status !== 403;
                    return response.json().then(data => ({ data, access }));
                })
                .then(({ data, access }) => {
                    console.log(access)
                    if (access) {
                        document.getElementById("data").textContent = "User Successfully Deleted";
                    } else {
                        document.getElementById("data").textContent = "Unauthorized.";
                    }
                })
        }
    </script>
</body>
</html>
