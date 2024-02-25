<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
<div class="form-container">
    <h2 id="pageTitle">Sign Up</h2>
    <form>
        <input type="text" id="name" class="input" placeholder="Full Name"><br>
        <input type="text" id="user" class="input" placeholder="Username"><br>
        <input type="password" id="pass" class="input" placeholder="Password">
    </form>
    <button class="submit" onclick="signup()">Sign Up</button>
    <p id="error"></p>
    <button onclick="switchToLogin()">Switch to Login</button>
</div>
<script>
function switchToLogin() {
    window.location.href = "{{site.baseurl}}/login.html";
}
function signup() {
    data = {
        "name": document.getElementById("name").value,
        "uid": document.getElementById("user").value,
        "password": document.getElementById("pass").value,
        }
    let options = {
    method: 'POST',
    headers: {
        'Content-Type':
            'application/json;charset=utf-8'
    },
    body: JSON.stringify(data)
}
    let sign_up = fetch("{{site.backendurl}}/api/users/", options);
    sign_up.then(response => {
        if (response.status === 200) {
            window.location.href = "{{site.baseurl}}/login.html"
        }
        else if (response.status === 400) {
            document.getElementById("error").innerHTML = "You already have an account! Go to the login page."
        }
    }
        ) 
}
</script>
</html>
