<html>
<head>
    <title>Diabetes Predictor</title>
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
    <p id="diabetic"></p>
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
                    document.getElementById("diabetic").innerHTML = result;
                })
                .catch(error => {
                    console.error('Error:', error);
                });
        }
    </script>
</body>
</html>
