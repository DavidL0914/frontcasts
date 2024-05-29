<form>
<input id = "query" class = "input" placeholder="Search, ex. 'bananas' ">
</form>
<form>
<input id = "userQuery" class = "input" placeholder="Username">
</form>
<link rel="stylesheet" href="/frontcasts/assets/css/style.css">
 <!--This stylesheet was worked on by myself and my group mates.-->
<button class = "submit" onclick = "search()">Search 🔍</button>
<div id = "recipediv">Search something...greatness awaits!</div>
<div class="info-container">
<div class="info">
<p id="info" class="info-text">Welcome to the recipe searcher!
<br>Input any prompt into the text box,
<br>and click the search button.
<br>The results will include the name of the dish with a 
<br>button to show the recipe, and an image of the finished product.
<br></p>
</div>
</div>
<div id = "instructions" class = "instructions"></div>

<script>
        const api_key = "bda6dcbd9ea9479995b632addb9f3761";
        const options = {
            method: 'GET',
            headers: {
                'Content-Type': 'application/json;charset=utf-8',
            },
        };

        function search() {
            document.querySelector(".info").style.display = "none";
            const query = document.getElementById("query").value;
            const search_api_url = `https://api.spoonacular.com/recipes/complexSearch?apiKey=${api_key}&query=${query}`;
            fetch(search_api_url, options)
                .then(response => response.json())
                .then(data => {
                    displayRecipes(data.results);
                })
                .catch(error => {
                    console.error(error);
                });
        }
        let currentPage = 1;
        const recipesPerPage = 2;
        function star(id, starCount, uid) {
            const log_api_url = "{{site.backendurl}}/api/users/recipe";
            const data = {
                "id": id,
                "starCount": starCount,
                "uid": uid,
            };
            const options = {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json;charset=utf-8',
                },
                body: JSON.stringify(data),
                credentials: 'include'
            };
            console.log(JSON.stringify(data))
            console.log(data)
            fetch(log_api_url, options)
                .then(response => response.json())
                .then(data => {
                    console.log(data);
                })
                .catch(error => {
                    console.error(error);
                });
        }

        function fetchinfo(id) {
            const recipeList = document.getElementById("recipediv");
            if (recipeList.innerHTML != "") {
                recipeList.innerHTML = "";
            }
            const info_api_url = `https://api.spoonacular.com/recipes/${id}/information?apiKey=${api_key}`;
            fetch(info_api_url, options)
                .then(response => response.json())
                .then(data => {
                    recipeList.innerHTML += "<strong>Ingredients for " + data.title + "</strong><br><br><ul>";
                    data.extendedIngredients.forEach(ing => {
                        recipeList.innerHTML += "<li>" + ing.name + "</li>";
                    });
                    recipeList.innerHTML += "<br></ul>";
                    recipeList.innerHTML += "<strong>Instructions for " + data.title + "</strong><br><br><ul>";
                    recipeList.innerHTML += "<ul><li>" + data.instructions + "</li></ul>";
                    recipeList.innerHTML += "<br></ul>";
                });
        }

        function displayRecipes(recipes) {
            const uid = document.getElementById("userQuery").value;
            const recipeList = document.getElementById("recipediv");
            recipeList.innerHTML = "";
            const startIndex = (currentPage - 1) * recipesPerPage;
            const endIndex = startIndex + recipesPerPage;
            const recipesToShow = recipes.slice(startIndex, endIndex);
            recipesToShow.forEach(recipe => {
                const recipeDiv = document.createElement("div");
                const image = document.createElement("img");
                recipeDiv.classList.add("recipe");
                image.src = recipe.image;
                image.alt = recipe.title;
                image.setAttribute('draggable', false);
                const titleLink = document.createElement("button");
                titleLink.addEventListener('click', () => {
                    fetchinfo(recipe.id);
                });
                const starDiv = document.createElement("div");
                for (let i = 1; i <= 5; i++) {
                    const starButton = document.createElement("button");
                    starButton.textContent = "⭐";
                    starButton.addEventListener('click', () => {
                        star(recipe.id, i, uid);
                    });
                    starDiv.appendChild(starButton);
                }
                titleLink.textContent = recipe.title;
                const title = document.createElement("h3");
                title.appendChild(titleLink);
                recipeDiv.appendChild(title);
                recipeDiv.appendChild(image);
                recipeDiv.appendChild(starDiv);
                recipeList.appendChild(recipeDiv);
            });

            const totalPages = Math.ceil(recipes.length / recipesPerPage);
            const pageDiv = document.createElement("div");
            pageDiv.classList.add("page");
            const prevButton = document.createElement("button");
            prevButton.textContent = "<<";
            prevButton.addEventListener("click", () => {
                if (currentPage > 1) {
                    currentPage--;
                    displayRecipes(recipes);
                }
            });
            pageDiv.appendChild(prevButton);
            const nextButton = document.createElement("button");
            nextButton.textContent = ">>";
            nextButton.addEventListener("click", () => {
                if (currentPage < totalPages) {
                    currentPage++;
                    displayRecipes(recipes);
                } else {
                    window.alert("There are no more pages to show.");
                }
            });
            pageDiv.appendChild(nextButton);
            recipeList.appendChild(pageDiv);
        }
    </script>