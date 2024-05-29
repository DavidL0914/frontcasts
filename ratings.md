<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>User Ratings</title>
    <link rel="stylesheet" href="/frontcasts/assets/css/style.css">
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f0f0;
            margin: 0;
            padding: 20px;
        }
        h1 {
            text-align: center;
        }
        .controls {
            text-align: center;
            margin-bottom: 20px;
        }
        .rating {
            background-color: #ffffff;
            margin-bottom: 20px;
            padding: 20px;
            border: 1px solid #ddd;
            border-radius: 5px;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        }
        .rating img {
            max-width: 100px;
            max-height: 100px;
            display: block;
            margin-top: 10px;
        }
    </style>
</head>
<body>
    <h1>User Ratings</h1>
    <div class="controls">
        <label for="sort-options">Sort by: </label>
        <select id="sort-options">
            <option value="alphabet">Alphabetical by Username</option>
            <option value="user">Group by User</option>
            <option value="rating">Highest to Lowest Rating</option>
        </select>
    </div>
    <div id="ratings-div"></div>
    <script>
        document.addEventListener('DOMContentLoaded', () => {
            fetchRatings();
            document.getElementById('sort-options').addEventListener('change', () => {
                fetchRatings();
            });
        });
        function fetchRatings() {
            const ratings_api_url = "{{site.backendurl}}/api/users/ratings";
            fetch(ratings_api_url, {
                method: 'GET',
                headers: {
                    'Content-Type': 'application/json;charset=utf-8',
                }
            })
            .then(response => {
                console.log(JSON.parse(response));
                return response;
            })
            .then(data => {
                fetchAllRecipesInfo(data);
            })
            .catch(error => {
                console.error(error);
            });
        }
        function fetchAllRecipesInfo(ratingsData) {
            const recipeIds = Object.keys(ratingsData);
            const recipePromises = recipeIds.map(recipeId => fetchRecipeInfo(recipeId));
            Promise.all(recipePromises)
                .then(recipes => {
                    const combinedData = recipes.map(recipe => {
                        const ratingInfo = ratingsData[recipe.id];
                        return {
                            recipe,
                            ratingInfo
                        };
                    });
                    sortAndDisplayRatings(combinedData);
                })
                .catch(error => {
                    console.error(error);
                });
        }
        function sortAndDisplayRatings(combinedData) {
            const sortOption = document.getElementById('sort-options').value;
            if (sortOption === 'alphabet') {
                combinedData.sort(alphabeticalSort);
            } else if (sortOption === 'user') {
                combinedData = groupByUserSort(combinedData);
            } else if (sortOption === 'rating') {
                combinedData.sort(ratingSort);
            }
            displayRatings(combinedData);
        }
        function alphabeticalSort(a, b) {
            return a.ratingInfo.uid.localeCompare(b.ratingInfo.uid);
        }
        function ratingSort(a, b) {
            return b.ratingInfo.starCount - a.ratingInfo.starCount;
        }
        function groupByUserSort(data) {
            const groupedData = {};
            data.forEach(item => {
                const userId = item.ratingInfo.uid;
                if (!groupedData[userId]) {
                    groupedData[userId] = [];
                }
                groupedData[userId].push(item);
            });
            const sortedGroupedData = [];
            Object.keys(groupedData).sort().forEach(userId => {
                sortedGroupedData.push(...groupedData[userId]);
            });
            return sortedGroupedData;
        }
        function displayRatings(sortedData) {
            const ratingsDiv = document.getElementById("ratings-div");
            ratingsDiv.innerHTML = "";
            sortedData.forEach(({ recipe, ratingInfo }) => {
                const ratingDiv = document.createElement("div");
                ratingDiv.classList.add("rating");
                const userHeader = document.createElement("h3");
                userHeader.textContent = `User ID: ${ratingInfo.uid}`;
                ratingDiv.appendChild(userHeader);
                const recipeTitle = document.createElement("p");
                recipeTitle.textContent = `Recipe: ${recipe.title}`;
                ratingDiv.appendChild(recipeTitle);
                const stars = document.createElement("p");
                stars.textContent = `Rating: ${"⭐".repeat(ratingInfo.starCount)}`;
                ratingDiv.appendChild(stars);
                const recipeImage = document.createElement("img");
                recipeImage.src = recipe.image;
                recipeImage.alt = recipe.title;
                ratingDiv.appendChild(recipeImage);
                ratingsDiv.appendChild(ratingDiv);
            });
        }
        function fetchRecipeInfo(recipeId) {
            const recipe_api_url = `https://api.spoonacular.com/recipes/${recipeId}/information?apiKey=bda6dcbd9ea9479995b632addb9f3761`;
            return fetch(recipe_api_url, {
                method: 'GET',
                headers: {
                    'Content-Type': 'application/json;charset=utf-8',
                }
            })
            .then(response => response.json())
            .catch(error => {
                console.error(error);
                throw error;
            });
        }
    </script>
</body>
</html>
