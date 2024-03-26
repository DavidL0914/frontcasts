<html>
<head>
    <title>Diabetes Predictor</title>
    <style>
        body {
            font-family: Arial, sans-serif; /* Set default font family */
            background: url('https://images.pexels.com/photos/1640772/pexels-photo-1640772.jpeg?cs=srgb&dl=pexels-ella-olsson-1640772.jpg&fm=jpg') center center fixed;
            background-size: cover;
            color: black; /* Set default text color to black */
            margin: 0; /* Remove default margin */
            padding: 0; /* Remove default padding */
        }

        /* Container styling */
        .container {
            max-width: 400px; /* Set maximum width for better layout */
            margin: 50px auto; /* Center the container */
            padding: 20px; /* Add padding for better appearance */
            background-color: rgba(255, 255, 255, 0.9); /* Semi-transparent white background */
            border-radius: 10px; /* Add rounded corners */
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.3); /* Add box shadow for depth */
        }

        /* Style for the diabetes percent text */
        #diabetic {
            background-color: white; /* Set background color for the div */
            color: black; /* Set text color to black */
            font-size: 18px; /* Adjust font size for readability */
            font-weight: bold; /* Make text bold for emphasis */
            margin-top: 10px; /* Add some margin for spacing */
            padding: 10px; /* Add padding for better appearance */
            border-radius: 5px; /* Add rounded corners for the div */
        }

        /* Style for input boxes */
        input[type="text"] {
            width: calc(100% - 22px); /* Set input width to 100% minus padding and border */
            padding: 10px; /* Add padding for better appearance */
            margin-top: 10px; /* Add margin for spacing between inputs */
            border: none; /* Remove default border */
            border-radius: 5px; /* Add rounded corners for the input */
            box-sizing: border-box; /* Include padding and border in input's total width */
            background-color: #f2f2f2; /* Set background color for input */
        }

        /* Style for button */
        button {
            width: 100%; /* Set button width to 100% */
            padding: 10px; /* Add padding for better appearance */
            margin-top: 20px; /* Add margin for spacing */
            border: none; /* Remove default border */
            border-radius: 5px; /* Add rounded corners for the button */
            background-color: #3498db; /* Set background color for button */
            color: white; /* Set text color to white */
            cursor: pointer; /* Change cursor to pointer on hover */
        }

        /* Style for button on hover */
        button:hover {
            background-color: #45a049; /* Darken background color on hover */
        }
    </style>
</head>
<body>
    <div class="container">
        <h2 style="text-align: center;">Diabetes Predictor</h2>
        <form>
            <input id="height" type="text" placeholder="Height in cm">
            <input id="sugar" type="text" placeholder="Weekly sugar intake in g">
            <input id="activity" type="text" placeholder="Weekly activity in hours">
            <input id="bodyfat" type="text" placeholder="Approximate Bodyfat %">
            <input id="age" type="text" placeholder="Age">
        </form>
        <button onclick="predict()">Get Prediction %</button>
        <div id="result"></div> <!-- Placeholder for displaying result -->
    </div>
    <script>
        function predict() {
            var data = {
                "height": parseFloat(document.getElementById("height").value),
                "sugar": parseFloat(document.getElementById("sugar").value),
                "activity": parseFloat(document.getElementById("activity").value),
                "bodyfat": parseFloat(document.getElementById("bodyfat").value),
                "age": parseFloat(document.getElementById("age").value)
            };
            var options = {
                method: "POST",
                headers: {
                    'Content-Type': 'application/json;charset=utf-8'
                },
                body: JSON.stringify(data)
            };
            fetch("http://127.0.0.1:8008/api/predict/", options)
                .then(response => response.json())
                .then(result => {
                    // Display the result inside the div
                    document.getElementById("result").innerHTML = `
                        <div id="diabetic">${result}</div>
                    `;
                })
                .catch(error => {
                    console.error('Error:', error);
                });
        }
    </script>
</body>
</html>