<form>
<input id = "query" class = "input" placeholder="Search, ex. 'bananas' ">
</form>
<link rel="stylesheet" href="/frontcasts/assets/css/style.css">
<button class = "submit" onclick = "search()">Search 🔍</button>
<div id = "recipediv">Search something...greatness awaits!</div>
<div class="info-container">
<div class="info">
<p id="info" class="info-text">Welcome to our recipe searcher!<br>Input any prompt into the text box,<br>and click the search button.<br>The results will include the name of the dish with a <br>link to a recipe, and an image of the finished product.<br>The results of the search will appear to the left of this text!</p>
</div>
</div>
<br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br>
<div id = "instructions" class = "instructions"></div>
<script>
const api_key = "353ca5d1296e4a1187d417811123d58b";
const options = {
                method: 'GET',
                headers: {
                    'Content-Type': 'application/json;charset=utf-8',
                },
            };
function search() {
    document.querySelector(".info").style.display = "none"
    const query = document.getElementById("query").value;
    const api_key = "353ca5d1296e4a1187d417811123d58b";
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
const recipesPerPage = 2; // Adjust as needed
function fetchinfo(id) {
    if (document.getElementById("recipediv").innerHTML != ""){ 
        document.getElementById("recipediv").innerHTML = ""
    }
    const info_api_url = `https://api.spoonacular.com/recipes/${id}/information?apiKey=${api_key}`;
    fetch(info_api_url, options)
    .then(response => response.json())
    .then(data => {
        document.getElementById("recipediv").innerHTML += "<strong>Ingredients for " + data.title + "</strong>" + "<br><br><ul>"
        data.extendedIngredients.forEach(ing => {
            document.getElementById("recipediv").innerHTML += "<li>" + ing.name + "</li>"
        })
        document.getElementById("recipediv").innerHTML += "<br></ul>" 
        document.getElementById("recipediv").innerHTML += "<strong>Instructions for " + data.title + "</strong>" + "<br><br><ul>"
        document.getElementById("recipediv").innerHTML += "<ul><li>" + data.instructions + "</li></ul>"
        document.getElementById("recipediv").innerHTML += "<br></ul>" 
    }) 
}
function displayRecipes(recipes) {
    const recipeList = document.getElementById("recipediv");
    recipeList.innerHTML = ""; // Clear previous results
    // Calculate start and end index for current page
    const startIndex = (currentPage - 1) * recipesPerPage;
    const endIndex = startIndex + recipesPerPage;
    // Display recipes for the current page
    const recipesToShow = recipes.slice(startIndex, endIndex);
    recipesToShow.forEach(recipe => {
        const recipeDiv = document.createElement("div");
        const image = document.createElement("img");
        recipeDiv.classList.add("recipe");
        image.src = recipe.image;
        image.alt = recipe.title;
        image.setAttribute('draggable', false);
        // Create a link for the recipe title
        const titleLink = document.createElement("button");
        titleLink.addEventListener('click', () => {
            fetchinfo(recipe.id);
        });
        titleLink.textContent = recipe.title;
        const title = document.createElement("h3");
        title.appendChild(titleLink);
        recipeDiv.appendChild(title);
        recipeDiv.appendChild(image);
        recipeList.appendChild(recipeDiv);
    });
    // Add page controls
    const totalPages = Math.ceil(recipes.length / recipesPerPage);
    const pageDiv = document.createElement("div");
    pageDiv.classList.add("page");
    // Previous page button
    const prevButton = document.createElement("button");
    prevButton.textContent = "<<";
    prevButton.addEventListener("click", () => {
        if (currentPage > 1) {
            currentPage--;
            displayRecipes(recipes);
        }
    });
    pageDiv.appendChild(prevButton);
    // Next page button
    const nextButton = document.createElement("button");
    nextButton.textContent = ">>";
    nextButton.addEventListener("click", () => {
        if (currentPage < totalPages) {
            currentPage++;
            displayRecipes(recipes);
        }
    });
    pageDiv.appendChild(nextButton);
    recipeList.appendChild(pageDiv);
}


</script>