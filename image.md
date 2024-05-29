---
layout: wow
---
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Image Submission</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 50px;
            min-height: 100vh;
            background-color: #f5f5f5;
            overflow-y: scroll;
            height: 100%;
            background: url('https://images.pexels.com/photos/1640772/pexels-photo-1640772.jpeg?cs=srgb&dl=pexels-ella-olsson-1640772.jpg&fm=jpg') center center fixed;
            background-size: cover;
        }
        .container {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
        }
        .form-container {
            background-color: #fff;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            padding: 20px;
            margin-bottom: 20px;
            width: 400px;
            margin-top: 60px;
        }
        .data-container {
            background-color: #fff;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            padding: 20px;
            margin-bottom: 20px;
            width: 400px;
        }
        #pagination {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-top: 10px;
        }
        .input {
            width: 100%;
            padding: 10px;
            margin-bottom: 10px;
            box-sizing: border-box;
        }
        .submit {
            background-color: #3498db;
            color: #fff;
            border: none;
            padding: 10px;
            cursor: pointer;
            border-radius: 4px;
        }
        #error {
            color: red;
        }
        #data {
            margin-top: 20px;
        }
        .data-box {
            background-color: #e0e0e0;
            border: 1px solid #ccc;
            border-radius: 8px;
            padding: 10px;
            margin-bottom: 10px;
        }
        .data-box .content {
            display: flex;
            flex-direction: column;
        }
        .data-box .username {
            font-weight: bold;
            margin-bottom: 5px;
        }
        .input {
            width: 100%;
            padding: 8px;
            margin-bottom: 8px;
            box-sizing: border-box;
        }
        .data-box {
            padding: 8px;
            margin-bottom: 10px;
        }
        @media (max-width: 500px) {
            .form-container,
            .data-container {
                width: 100%;
            }
        }
        .headnav {
            background-color: #333;
            padding: 13px 0px;
            width: 100%;
            margin-bottom: 20px;
            left: 50%;
            display: block;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
        }
        .headnav a {
            text-decoration: none;
            color: #fff;
            font-weight: bold;
            font-size: 18px;
            text-align: left;
            margin: 0 10px;
            transition: color 0.3s ease-in-out;
        }
        .headnav a:hover {
            color: black;
        }
        nav {
            background-color: #333;
            overflow: hidden;
        }
        nav button {
            background-color: #333;
            float: left;  
            display: block;
            color: #333;
            border: none;
            text-align: center;
            padding: 14px 16px;
            text-decoration: none;
            transition: background-color 0.3s;
        }
        nav a {
            float: left;
            display: block;
            color: #333;
            text-align: center;
            padding: 14px 16px;
            text-decoration: none;
            transition: background-color 0.3s;
        }
        nav a:hover {
            background-color: #ddd;
            color: black;
        }
        nav a.active {
            background-color: #4CAF50;
            color: white;
        }
        nav button:hover {
            background-color: #ddd;
            color: black;
        }
        nav button.active {
            background-color: #4CAF50;
            color: white;
        }
        .thumbs-up {
            background-color: transparent;
            border: none;
            cursor: pointer;
            color: #3498db;
            font-size: 18px;
            display: flex;
            align-items: center;
        }
        .thumbs-up .count {
            margin-left: 5px;
        }
    </style>
</head>
<body>
    <nav class="headnav">
        <a onclick='window.location.href="{{site.baseurl}}/home.html"'>Home</a>
    </nav>
    <div class="container">
        <div class="form-container">
            <h2 id="pageTitle">Community Recipes!</h2>
            <form>
                <textarea id="image" class="input" placeholder="Share your own favorite recipes for others to try here"></textarea><br>
            </form>
            <button class="submit" onclick="submitImage()">Submit</button>
            <p id="error"></p>
        </div>
        <div class="data-container">
            <h2>Community Recipes</h2>
            <div id="data"></div>
            <div id="pagination">
                <button onclick="changePage(-1)">❮ Prev</button>
                <span id="pageNumber">Page 1</span>
                <button onclick="changePage(1)">Next ❯</button>
            </div>
        </div>
    </div>
    <script>
        function submitImage() {
            let imageContent = document.getElementById("image").value;

            if (!imageContent.trim()) {
                document.getElementById("error").innerHTML = "Please enter some text before submitting.";
                return;
            }

            let data = {
                "image": imageContent
            };

            let options = {
                method: 'PUT',
                headers: {
                    'Content-Type': 'application/json;charset=utf-8'
                },
                body: JSON.stringify(data),
                credentials: 'include'
            };

            fetch('http://127.0.0.1:8008/api/users/image', options)
                .then(response => {
                    if (response.ok) {
                        document.getElementById("error").innerHTML = "Post successfully uploaded";
                        // Initialize thumbs-up count to zero for the new post
                        localStorage.setItem(imageContent, 0);
                        fetchImages();
                    } else {
                        return response.json().then(errorData => {
                            if (errorData && errorData.message) {
                                document.getElementById("error").innerHTML = errorData.message;
                            } else {
                                document.getElementById("error").innerHTML = "Error submitting post";
                            }
                        });
                    }
                })
                .catch(error => {
                    console.error("Error:", error);
                    document.getElementById("error").innerHTML = "Error submitting image";
                });
        }

        function fetchImages() {
            let options = {
                method: 'GET',
                headers: {
                    'Content-Type': 'application/json;charset=utf-8'
                },
                credentials: 'include'
            };

            fetch("http://127.0.0.1:8008/api/users/name", options)
                .then(response => response.json())
                .then(usernames => {
                    fetch("http://127.0.0.1:8008/api/users/image", options)
                        .then(response => {
                            let access = response.status !== 403;
                            return response.json().then(data => ({ data, access, usernames }));
                        })
                        .then(({ data, access, usernames }) => {
                            if (access) {
                                let itemlist = [];
                                data.forEach((entry, index) => {
                                    if (entry.includes("///")) {
                                        let splitEntries = entry.split("///");
                                        itemlist = itemlist.concat(splitEntries.map(image => ({ image, username: usernames[index] })));
                                    } else {
                                        itemlist.push({ image: entry, username: usernames[index] });
                                    }
                                });

                                // Sort the itemlist based on the thumbs-up count, in descending order
                                itemlist.sort((a, b) => getUpvoteCount(b.image) - getUpvoteCount(a.image));

                                let dataContainer = document.getElementById("data");
                                dataContainer.innerHTML = "";

                                let startIndex = (currentPage - 1) * postsPerPage;
                                let endIndex = currentPage * postsPerPage;
                                let paginatedItems = itemlist.slice(startIndex, endIndex);

                                paginatedItems.forEach((item, index) => {
                                    if (item.image.trim() !== "") {
                                        let box = document.createElement("div");
                                        box.className = "data-box";

                                        let deleteButton = document.createElement("button");
                                        deleteButton.textContent = "Delete";
                                        deleteButton.onclick = function() {
                                            deletePost(item.image); // Capture the image content as key
                                        };

                                        let thumbsUpButton = document.createElement("button");
                                        thumbsUpButton.className = "thumbs-up";
                                        thumbsUpButton.onclick = function() {
                                            upvotePost(item.image); // Capture the image content as key
                                        };

                                        let countSpan = document.createElement("span");
                                        countSpan.className = "count";
                                        countSpan.id = `upvote-count-${index}`;
                                        thumbsUpButton.innerHTML = `👍 `;
                                        thumbsUpButton.appendChild(countSpan);

                                        let contentDiv = document.createElement("div");
                                        contentDiv.className = "content";

                                        let usernameParagraph = document.createElement("p");
                                        usernameParagraph.className = "username";
                                        usernameParagraph.textContent = item.username;

                                        let postParagraph = document.createElement("p");
                                        postParagraph.textContent = item.image;

                                        contentDiv.appendChild(usernameParagraph);
                                        contentDiv.appendChild(postParagraph);
                                        contentDiv.appendChild(deleteButton);
                                        contentDiv.appendChild(thumbsUpButton);

                                        box.appendChild(contentDiv);

                                        dataContainer.appendChild(box);

                                        let likesCount = getUpvoteCount(item.image); // Retrieve likes count from localStorage
                                        updateLikesCount(index, likesCount);
                                    }
                                });

                                updatePageNumber();

                                document.getElementById("pagination").style.display = itemlist.length > postsPerPage ? "flex" : "none";
                            } else {
                                document.getElementById("data").textContent = "Unauthorized.";
                            }
                        })
                        .catch(error => {
                            console.error("Error:", error);
                            document.getElementById("data").textContent = "Error fetching images";
                        });
                })
                .catch(error => {
                    console.error("Error:", error);
                    document.getElementById("data").textContent = "Error fetching usernames";
                });
        }

        function getUpvoteCount(image) {
            return parseInt(localStorage.getItem(image) || 0, 10);
        }

        function updateLikesCount(index, count) {
            let countSpan = document.getElementById(`upvote-count-${index}`);
            if (countSpan) {
                countSpan.textContent = count;
            }
        }

        function upvotePost(image) {
            let count = parseInt(localStorage.getItem(image) || 0, 10);
            count += 1;
            localStorage.setItem(image, count);

            // Update the count on the UI without re-fetching all posts
            fetchImages();
        }

        let currentPage = 1;
        const postsPerPage = 7;

        function changePage(direction) {
            currentPage += direction;
            currentPage = Math.max(1, currentPage);
            fetchImages();
        }

        function updatePageNumber() {
            document.getElementById("pageNumber").textContent = `Page ${currentPage}`;
        }

        function deletePost(image) {
            let data = {
                "image": image
            };

            let options = {
                method: 'DELETE',
                headers: {
                    'Content-Type': 'application/json;charset=utf-8'
                },
                body: JSON.stringify(data),
                credentials: 'include'
            };

            fetch('http://127.0.0.1:8008/api/users/image', options)
                .then(response => {
                    if (response.ok) {
                        document.getElementById("error").innerHTML = "Post successfully deleted";
                        // Remove thumbs-up count from localStorage for the deleted post
                        localStorage.removeItem(image);
                        fetchImages();
                    } else {
                        return response.json().then(errorData => {
                            if (errorData && errorData.message) {
                                document.getElementById("error").innerHTML = errorData.message;
                            } else {
                                document.getElementById("error").innerHTML = "Error deleting post";
                            }
                        });
                    }
                })
                .catch(error => {
                    console.error("Error:", error);
                    document.getElementById("error").innerHTML = "Error deleting image";
                });
        }

        window.onload = function () {
            currentPage = 1;
            fetchImages();
        };
    </script>
</body>
</html>