<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
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
        .input-container {
            position: relative;
            margin-bottom: 20px;
        }

        .unit-label {
            position: absolute;
            top: 50%;
            left: 10px;
            transform: translateY(-50%);
            color: black;
            font-size: 16px;
            font-weight: normal;
            transition: all 0.3s ease;
        }

        input[type="text"] {
            width: calc(100% - 22px); /* Set input width to 100% minus padding and border */
            padding: 10px; /* Add padding for better appearance */
            margin-top: 10px; /* Add margin for spacing between inputs */
            border: none; /* Remove default border */
            border-radius: 5px; /* Add rounded corners for the input */
            box-sizing: border-box; /* Include padding and border in input's total width */
            background-color: #f2f2f2; /* Set background color for input */
            transition: all 0.3s ease;
        }

        /* Animation for input placeholder */
        input[type="text"]:focus ~ .unit-label,
        input[type="text"]:not(:placeholder-shown) ~ .unit-label {
            top: 0;
            font-size: 12px;
            color: #3498db; /* Change color on focus */
            transition: all 0.3s ease;
        }

        input[type="text"]:focus::placeholder,
        input[type="text"]:not(:placeholder-shown)::placeholder {
            opacity: 0;
            transition: opacity 0.3s ease;
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
            background-color: #34749e; /* Darken background color on hover */
        }

        /* Add border bottom to focused input container */
        .focused {
            border-bottom: 2px solid #3498db;
        }
    </style>
</head>
<body>
    <div class="container">
        <h2 style="text-align: center;">Diabetes Predictor</h2>
        <form>
            <div class="input-container">
                <input id="height" type="text" placeholder=''>
                <label class="unit-label">Height (cm)</label>
            </div>
            <div class="input-container">
                <input id="sugar" type="text" placeholder="">
                <label class="unit-label">Weekly sugar intake (g)</label>
            </div>
            <div class="input-container">
                <input id="activity" type="text" placeholder="">
                <label class="unit-label">Weekly activity (hours)</label>
            </div>
            <div class="input-container">
                <input id="bodyfat" type="text" placeholder="">
                <label class="unit-label">Approximate Bodyfat (%)</label>
            </div>
            <div class="input-container">
                <input id="age" type="text" placeholder="">
                <label class="unit-label">Age (years)</label>
            </div>
        </form>
        <button onclick="predict()">Get Prediction %</button>
        <div id="result"></div> <!-- Placeholder for displaying result -->
    </div>
<script>
    function predict() {
        // Get input values
        var height = parseFloat(document.getElementById("height").value);
        var sugar = parseFloat(document.getElementById("sugar").value);
        var activity = parseFloat(document.getElementById("activity").value);
        var bodyfat = parseFloat(document.getElementById("bodyfat").value);
        var age = parseFloat(document.getElementById("age").value);

        // Check if any input value is invalid
        if (isNaN(height) || isNaN(sugar) || isNaN(activity) || isNaN(bodyfat) || isNaN(age)) {
            // Display error message
            document.getElementById("result").innerHTML = `
                <div id="diabetic" style="color: red;">Please enter valid numeric values for all inputs.</div>
            `;
            return; // Stop execution
        }

        // Prepare data for prediction request
        var data = {
            "height": height,
            "sugar": sugar,
            "activity": activity,
            "bodyfat": bodyfat,
            "age": age
        };

        var options = {
            method: "POST",
            headers: {
                'Content-Type': 'application/json;charset=utf-8'
            },
            body: JSON.stringify(data)
        };

        // Send prediction request
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

    // JavaScript to handle focus event on input boxes
    document.addEventListener("DOMContentLoaded", function() {
        var inputContainers = document.querySelectorAll('.input-container input');
        inputContainers.forEach(function(input) {
            input.addEventListener('focus', function(event) {
                event.target.parentNode.classList.add('focused');
            });
            input.addEventListener('blur', function(event) {
                if (!event.target.value.trim()) {
                    event.target.parentNode.classList.remove('focused');
                }
            });
        });
    });
</script>
</body>
</html>
