# Motivation

GitHub is one of the most widely used platforms for sharing and discovering open-source software. One of its most visible features is the star count, which is often used as a quick indicator of a repository’s popularity and, implicitly, its quality. Developers, students, and even companies frequently rely on stars when deciding which libraries or tools to use.

However, GitHub stars are a social signal: they can be influenced by visibility, timing, or community hype rather than by the underlying engineering quality of the code. This raises a natural question:

**Do GitHub stars actually reflect software quality?**

This project explores that question by collecting and analyzing data from GitHub repositories and comparing popularity metrics with measurable indicators of code quality.

# Dataset Overview

To investigate this question, I created a custom dataset using the GitHub REST API. Rather than relying on an existing dataset, I collected the data myself to maintain full control over the scope, selection criteria, and structure of the data.

The dataset consists of 30 public GitHub repositories, selected according to the following criteria:

- Primary programming language: Python
- Sorted by: number of GitHub stars
- Order: descending
- Data source: GitHub Search API
- Repository visibility: public repositories only

This selection reflects repositories that developers are most likely to encounter when searching for Python projects on GitHub.

# Data Collection Process

Access to the GitHub API was authenticated using a personal access token, allowing a higher request limit and more reliable data retrieval. A search request was made to the GitHub Search API endpoint with parameters specifying the programming language (Python), sorting by star count, and limiting the result size to 30 repositories.

For each repository returned by the API, the full metadata object was retrieved and stored locally. The raw data includes information such as:

- Repository name and URL
- Star, fork, and watcher counts
- Creation and last update timestamps
- Declared programming language
- Issue statistics and contributor endpoints

The complete, unmodified API response was saved as a raw JSON file, ensuring transparency and reproducibility. This raw dataset serves as the foundation for all subsequent data processing and analysis.

# Reproducibility and Ethics

All data used in this project is publicly available and legally shareable under GitHub’s terms of service. No personal or sensitive data was collected. By storing the raw data separately from processed data and analysis code, the project ensures that results can be reproduced and inspected independently.