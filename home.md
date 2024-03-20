---
layout: home
search_exclude: true
---

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Navigation Bar</title>
    <link rel="stylesheet" href="frontcasts-styling.scss">
</head>
<body>
f
<nav>
    <a onclick='window.location.href="{{site.baseurl}}/signup"'>Signup</a>
    <a onclick='window.location.href="{{site.baseurl}}/edit"'>Edit</a>
    <a onclick='window.location.href="{{site.baseurl}}/data"'>Data</a>
    <a onclick='window.location.href="{{site.baseurl}}/settings"'>User Settings</a>
    <a onclick='window.location.href="{{site.baseurl}}/image.html"'>Forum</a>
    <a onclick='window.location.href="{{site.baseurl}}/search.html"'>Search</a>
    <a onclick='window.location.href="{{site.baseurl}}/image"'>Share</a>
    <a onclick='window.location.href="{{site.baseurl}}/ML.html"'>Diabetes!</a>
    <button class = "logoutbutton" onclick="eraseCookie()">Logout</button>
</nav>

<!-- Your page content goes here -->

<script>
    function eraseCookie() {   
        document.cookie = 'jwt=; Max-Age=0; path=/; domain=' + location.hostname;
        console.log(document.cookie) 
        window.location.reload()
    }

    // Function to get the cookie value by name
    function getCookie(name) {
        var match = document.cookie.match(RegExp('(?:^|;\\s*)' + name + '=([^;]*)')); 
        return match ? match[1] : null;
    }

    // Check if the JWT cookie exists on page load
    addEventListener("load", (event) => {
        console.log(getCookie("jwt"))
        if(getCookie("jwt")){
            return
        }
        else {
            window.location.href = "{{site.baseurl}}/login.html"
        }
    })

    // Retrieve and apply theme preference from local storage
    document.addEventListener('DOMContentLoaded', function() {
        const currentTheme = localStorage.getItem('theme') || 'light'; // Default to 'light' theme if no preference is found
        document.body.classList.toggle('dark-theme', currentTheme === 'dark');
    });
</script>

</body>
</html>
