<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
<div>
<div id = "data"></div>
</div>
</html>
<script>
window.onload = getData()
function getData() {
    let options = {
    method: 'GET',
    headers: {
        'Content-Type': 'application/json;charset=utf-8',
    },
    credentials: 'include'
}
    fetch("{{site.backendurl}}/api/users/", options)
        .then(response => {
            let access = response.status !== 401 && response.status !== 403;
            return response.json().then(data => ({ data, access }));
        })
        .then(({data, access}) => {
            console.log(access)
            if (access){ 
            document.getElementById("data").innerHTML = "<strong>Data for all users<br><br></strong>" + generateHTMLTable(data);
            }
            else {
                document.getElementById("data").textContent = "Unauthorized.";
            }
        })
}
function generateHTMLTable(data) {
    let htmlTable = "<table><thead><tr><th>ID</th><th>Image</th><th>Name</th><th>Role</th><th>UID</th></tr></thead><tbody>";
    data.forEach(item => {
        htmlTable += `<tr><td>${item.id}</td><td>${item.image}</td><td>${item.name}</td><td>${item.role}</td><td>${item.uid}</td></tr>`;
    });
    htmlTable += "</tbody></table>";
    return htmlTable;
}
</script>