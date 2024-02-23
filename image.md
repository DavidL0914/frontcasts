<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Image Submission</title>
        <style>
            /* Your CSS code goes here */
            body {
                font-family: Arial, sans-serif;
                margin: 0;
                padding: 0;
                display: flex;
                flex-direction: column;
                align-items: center;
                justify-content: center;
                min-height: 100vh;
                background-color: #f5f5f5;
                overflow-y: ;
            }

            .form-container {
                background-color: #fff;
                border-radius: 8px;
                box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
                padding: 20px;
                margin-bottom: 20px;
            }

            .data-container {
                background-color: #fff;
                border-radius: 8px;
                box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
                padding: 20px;
            }

            .input {
                width: 100%;
                padding: 10px;
                margin-bottom: 10px;
                box-sizing: border-box;
            }

            .submit {
                background-color: #4caf50;
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
                white-space: pre-wrap;
            }

            /* Styles for individual boxes */
            .data-box {
                background-color: #e0e0e0;
                border: 1px solid #ccc;
                border-radius: 8px;
                padding: 10px;
                margin-bottom: 10px;
            }
        </style>
    </head>
    <body>
        <div class="form-container">
            <h2 id="pageTitle">Submit Image</h2>
            <form>
                <textarea id="image" class="input" placeholder="Enter your text here"></textarea><br>
            </form>
            <button class="submit" onclick="submitImage()">Submit</button>
            <p id="error"></p>
        </div>
        <div class="data-container">
            <h2>Image Data</h2>
            <div id="data"></div>
        </div>
        <script>
            function submitImage() {
                // Get the text content from the textarea
                let imageContent = document.getElementById("image").value;
                // Ensure the text content is not empty
                if (!imageContent.trim()) {
                    document.getElementById("error").innerHTML = "Please enter some text before submitting.";
                    return;
                }
                // Create an object with the text data and a unique UID (timestamp)
                let data = {
                    "image": imageContent
                };
                // Configure fetch options
                let options = {
                    method: 'PUT',
                    headers: {
                        'Content-Type': 'application/json;charset=utf-8'
                    },
                    body: JSON.stringify(data),
                    credentials: 'include'
                };
                // Send the text data to the backend
                fetch('http://127.0.0.1:8008/api/users/image', options)
                    .then(response => {
                        if (response.ok) {
                            // Handle successful submission
                            document.getElementById("error").innerHTML = "Image submitted successfully";
                            // Fetch updated images after submission
                            fetchImages();
                        } else {
                            // Handle submission error
                            return response.json().then(errorData => {
                                if (errorData && errorData.message) {
                                    document.getElementById("error").innerHTML = errorData.message;
                                } else {
                                    document.getElementById("error").innerHTML = "Error submitting image";
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
        
        // Fetch usernames first
        fetch("http://127.0.0.1:8008/api/users/name", options)
            .then(response => response.json())
            .then(usernames => {
                // Now fetch image data
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

                            let dataContainer = document.getElementById("data");
                            dataContainer.innerHTML = "";  // Clear previous content

                            // Create a box for each item in the array, only if the item is not empty
                            itemlist.forEach(item => {
                                if (item.image.trim() !== "") {
                                    let box = document.createElement("div");
                                    box.className = "data-box";
                                    // Display both image and username
                                    box.textContent = `${item.image} - ${item.username}`;
                                    dataContainer.appendChild(box);
                                }
                            });
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

            // Call the fetchImages function when the page loads
            window.onload = fetchImages;
        </script>
    </body>
    </html>