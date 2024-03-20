<html>
<head>
    <title>Diabetes Predictor</title>
    <style>
        body {
            background: url('https://images.pexels.com/photos/1640772/pexels-photo-1640772.jpeg?cs=srgb&dl=pexels-ella-olsson-1640772.jpg&fm=jpg') center center fixed;
            background-size: cover;
            color: black; /* Set default text color to black */
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
    </style>
</head>
<body>
    <form>
        <input id="height" placeholder="Height in cm">
        <input id="sugar" placeholder="Weekly sugar intake in g">
        <input id="activity" placeholder="Weekly activity in hours">
        <input id="weight" placeholder="Weight in kg">
        <input id="age" placeholder="Age">
    </form>
    <button onclick="predict()">Get Prediction %</button>
    <div id="result"></div> <!-- Placeholder for displaying result -->
    <script>
        function predict() {
            var data = {
                "height": parseFloat(document.getElementById("height").value),
                "sugar": parseFloat(document.getElementById("sugar").value),
                "activity": parseFloat(document.getElementById("activity").value),
                "weight": parseFloat(document.getElementById("weight").value),
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