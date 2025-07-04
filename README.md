<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Rajneesh Search</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background: #ffffff;
      margin: 0;
      padding: 60px 20px;
      text-align: center;
    }

    .profile-wrapper {
      display: flex;
      flex-direction: column;
      align-items: center;
      margin-bottom: 30px;
    }

    .profile-img {
      width: 64px;
      height: 64px;
      border-radius: 50%;
      object-fit: cover;
      box-shadow: 0 2px 6px rgba(0, 0, 0, 0.2);
      margin-bottom: 10px;
    }

    .name-img {
      width: 150px;
      opacity: 0.95;
    }

    .search-form {
      max-width: 600px;
      margin: auto;
      position: relative;
    }

    .search-box-wrapper {
      display: flex;
      align-items: center;
      border: 1px solid #dfe1e5;
      border-radius: 30px;
      padding: 10px 18px;
      background-color: #fff;
      transition: all 0.2s ease-in-out;
      box-shadow: 0 1px 6px rgba(32, 33, 36, 0.28);
    }

    .search-box-wrapper:hover {
      box-shadow: 0 2px 8px rgba(32, 33, 36, 0.35);
    }

    .search-box {
      flex: 1;
      font-size: 16px;
      border: none;
      outline: none;
      padding: 8px;
    }

    .suggestions {
      text-align: left;
      background: white;
      border: 1px solid #ccc;
      border-top: none;
      max-height: 250px;
      overflow-y: auto;
      border-radius: 0 0 20px 20px;
      position: absolute;
      width: 100%;
      z-index: 999;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
      animation: fadeIn 0.2s ease-in;
    }

    .suggestion {
      padding: 12px 16px;
      cursor: pointer;
      font-size: 15px;
      transition: background 0.2s;
    }

    .suggestion:hover {
      background-color: #f8f9fa;
    }

    @keyframes fadeIn {
      from {opacity: 0;}
      to {opacity: 1;}
    }
  </style>
</head>
<body>

  <!-- Profile and Name -->
  <div class="profile-wrapper">
    <img src="profile.jpg" class="profile-img" alt="Profile">
    <img src="rajneesh.png" class="name-img" alt="Rajneesh">
  </div>

  <!-- Search Form -->
  <form class="search-form" action="https://www.google.com/search" method="GET" target="_blank" id="searchForm">
    <div class="search-box-wrapper">
      <input type="text" name="q" placeholder="Search Google" class="search-box" id="searchInput" autocomplete="off">
    </div>
    <div class="suggestions" id="suggestions"></div>
  </form>

  <!-- Suggestion Script -->
  <script>
    const searchInput = document.getElementById("searchInput");
    const suggestionsBox = document.getElementById("suggestions");
    const form = document.getElementById("searchForm");

    let controller;

    searchInput.addEventListener("input", async () => {
      const query = searchInput.value.trim();

      if (!query) {
        suggestionsBox.innerHTML = "";
        return;
      }

      if (controller) controller.abort();  // Cancel previous request
      controller = new AbortController();

      try {
        const response = await fetch(`https://suggestqueries.google.com/complete/search?client=firefox&q=${query}`, {
          signal: controller.signal
        });

        const data = await response.json();
        const suggestions = data[1];

        suggestionsBox.innerHTML = "";
        suggestions.forEach((suggestion) => {
          const div = document.createElement("div");
          div.textContent = suggestion;
          div.classList.add("suggestion");
          div.onclick = () => {
            searchInput.value = suggestion;
            form.submit();
          };
          suggestionsBox.appendChild(div);
        });
      } catch (error) {
        suggestionsBox.innerHTML = "";
      }
    });

    // Hide suggestions when clicking outside
    document.addEventListener("click", (e) => {
      if (!e.target.closest(".search-form")) {
        suggestionsBox.innerHTML = "";
      }
    });
  </script>

</body>
</html>
