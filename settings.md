<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>User Settings</title>
    <link rel="stylesheet" href="frontcasts-styling.scss">
</head>
<body>
    <div class="container">
        <h1>User Settings</h1>
        <div class="settings-form">
            <span>Theme:</span>
            <button id="toggle-theme">Light</button>
            <br>
            <h2>Edit Username</h2>
            <label for="uid">Your User ID:</label>
            <input type="text" id="uid" name="uid">
            <br>
            <label for="new-username">New Username:</label>
            <input type="text" id="new-username" name="new-username">
            <br>
            <button id="save-changes">Save Changes</button>
            <div id="edit-notification"></div>
        </div>
    </div>
    <script>
        document.addEventListener('DOMContentLoaded', function() {
            // Function to set the theme based on saved preference
            function setTheme(theme) {
                const themeButton = document.getElementById('toggle-theme');
                const body = document.body;
                if (theme === 'dark') {
                    themeButton.textContent = 'Dark';
                    body.classList.add('dark-theme');
                } else {
                    themeButton.textContent = 'Light';
                    body.classList.remove('dark-theme');
                }
            }
            // Check if theme preference is saved in local storage
            const savedTheme = localStorage.getItem('theme');
            if (savedTheme) {
                setTheme(savedTheme);
            }
            // Toggle theme functionality
            document.getElementById('toggle-theme').addEventListener('click', function() {
                const currentTheme = document.getElementById('toggle-theme').textContent;
                const newTheme = currentTheme === 'Light' ? 'dark' : 'light';
                setTheme(newTheme);
                // Save theme setting to local storage
                localStorage.setItem('theme', newTheme);
            });
            // Save settings functionality
            document.getElementById('save-settings').addEventListener('click', function() {
                const username = document.getElementById('username').value;
                const password = document.getElementById('password').value;
                const theme = document.getElementById('toggle-theme').textContent; // Get the theme text directly from the button
                // Send settings data to backend
                saveSettings({ username: username, password: password, theme: theme });
            });
            function saveSettings(data) {
                fetch("{{site.backendurl}}/api/users/save_settings", {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify(data)
                })
                .then(response => {
                    if (response.ok) {
                        document.getElementById('notification').textContent = 'Settings saved successfully!';
                    } else {
                        document.getElementById('notification').textContent = 'Failed to save settings.';
                    }
                })
                .catch(error => {
                    console.error('Error saving settings:', error);
                    document.getElementById('notification').textContent = 'Failed to save settings.';
                });
            }
            // Save changes functionality
            document.getElementById('save-changes').addEventListener('click', function() {
                const uid = document.getElementById('uid').value;
                const newUsername = document.getElementById('new-username').value;
                // Send data to backend for updating user
                updateUsername(uid, newUsername);
            });
            function updateUsername(uid, newUsername) {
                const data = {
                    "uid": uid,
                    "name": newUsername
                };
                fetch("{{site.backendurl}}/api/users/", {
                    method: 'PUT',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify(data)
                })
                .then(response => {
                    if (response.ok) {
                        document.getElementById('edit-notification').textContent = 'Username updated successfully!';
                    } else {
                        document.getElementById('edit-notification').textContent = 'Failed to update username.';
                    }
                })
                .catch(error => {
                    console.error('Error updating username:', error);
                    document.getElementById('edit-notification').textContent = 'Failed to update username.';
                });
            }
        });
    </script>
</body>
</html>
