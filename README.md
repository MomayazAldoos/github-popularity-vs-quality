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

# 6. Defining Software Quality

Software quality is an abstract concept that cannot be directly observed or measured through a single variable. In the context of open-source development, “quality” can refer to usability, reliability, maintainability, or adherence to good engineering practices. Since the GitHub API does not provide a direct quality indicator, this project operationalizes software quality by defining a set of measurable proxy metrics.

The goal is not to claim that these metrics fully capture true software quality, but rather to construct a transparent, reproducible, and reasonable approximation that allows meaningful comparison across repositories.

## 6.1 Quality Dimensions

To keep the analysis interpretable and realistic, software quality is defined using three complementary dimensions:

1. Documentation quality
2. Maintenance quality
3. Engineering practices

These dimensions were chosen because they are commonly cited in software engineering literature, relevant to real-world usage, and feasible to measure using publicly available repository data.

## 6.2 Documentation Quality

Documentation plays a crucial role in making software accessible and usable, especially for new users. Well-documented projects reduce the learning curve and signal maturity and professionalism.

In this project, documentation quality is measured using the following indicators:

- Presence of a README file: A minimal requirement for understanding the purpose and usage of a repository.
- README length (word count): Used as a rough proxy for the level of detail provided in the documentation.
- Presence of a license file: Indicates that the project is intended for reuse and distribution.

These indicators are simple, objective, and reproducible, making them suitable proxies for documentation quality.

## 6.3 Maintenance Quality

Active maintenance is essential for the long-term reliability and security of software projects. Repositories that are frequently updated and supported by multiple contributors are more likely to remain functional and relevant over time.

Maintenance quality is assessed using the following metrics:

- Days since last commit: Measures how recently the repository was updated.
- Commits per month: Calculated by dividing the total number of commits by the age of the repository in months, capturing development activity over time.
- Number of contributors: Reflects the size of the developer community involved in maintaining the project.

Together, these metrics provide insight into how actively a project is maintained.

## 6.4 Engineering Practices

Engineering practices indicate whether a repository follows modern and professional software development workflows. While not exhaustive, certain structural signals can suggest a higher level of engineering discipline.

The following binary indicators are used:

- Presence of a test directory: Suggests that automated testing is part of the development process.
- Presence of continuous integration (CI) configuration: Detected via workflow files in the `.github/workflows` directory, indicating automated testing or checks.
- Modular code structure: Approximated by checking whether the repository contains multiple source files rather than a single monolithic file.

These indicators provide a lightweight assessment of engineering quality that can be applied consistently across repositories.

## 6.5 Limitations

It is important to note that the chosen metrics do not capture all aspects of software quality. For example, code readability, algorithmic efficiency, and architectural design are not directly measured. Furthermore, the analysis identifies associations rather than causal relationships.

Despite these limitations, the defined quality metrics offer a practical and transparent framework for exploring how repository popularity relates to observable engineering characteristics.

# 7. Data Cleaning, Transformation, and Missing Data Handling

The raw data collected from the GitHub REST API consists of nested JSON objects containing extensive metadata for each repository. While this raw format preserves all information returned by the API, it is not suitable for direct analysis or visualization. Therefore, a dedicated data cleaning and transformation step was performed to construct an analysis-ready dataset.

## 7.1 Data Selection and Restructuring

Each repository returned by the GitHub Search API was processed individually. From the full set of available metadata, only variables relevant to repository popularity, activity, and basic characteristics were retained. These include repository identifiers, popularity indicators (such as stars and forks), issue counts, and timestamps related to repository creation and updates.

Unnecessary or redundant fields were discarded. This restructuring step ensures that each row in the dataset represents exactly one repository, while each column corresponds to a single, clearly defined variable.

## 7.2 Feature Engineering

In addition to extracting existing metadata, new time-based features were computed to better describe repository activity and maturity:

- Repository age (days) was calculated as the number of days between the repository’s creation date and the current date.
- Days since last update was calculated as the number of days since the most recent update to the repository.

GitHub timestamps follow the ISO 8601 standard. To ensure compatibility with Python’s datetime parsing, the UTC timezone indicator (Z) was removed, while the standard date–time separator (T) was preserved. This allowed reliable date parsing and accurate time-difference calculations.

## 7.3 Handling Missing Data

Some metadata fields provided by the GitHub API may be missing for certain repositories. This is expected, as not all projects expose complete or standardized information. Missing data was handled explicitly and conservatively to avoid introducing bias or artificial values.

For categorical variables, such as the declared primary programming language, missing values were labeled as “Unknown”. This approach preserves all observations while clearly indicating the absence of information.

Numeric popularity and activity metrics (including star counts, forks, and watchers) were not imputed. Missing values in these fields would indicate underlying data inconsistencies rather than meaningful absence, and were therefore left unchanged. This decision prioritizes transparency and data integrity.

## 7.4 Final Dataset Structure and Reproducibility

The final cleaned dataset is stored in a flat, tabular CSV format, separate from the raw JSON data. This separation ensures reproducibility and traceability: the raw data remains unchanged as a reference, while the processed dataset serves as the basis for all subsequent analysis and visualization.

The resulting dataset is fully analysis-ready and forms the foundation for the extraction and evaluation of software quality metrics in the next stage of the project.

# 8. Extracting Software Quality Indicators

To operationalize software quality, additional repository-level indicators were extracted using the GitHub Contents API. For each repository in the cleaned dataset, the repository owner and name were used to query the API for a listing of files and directories in the repository’s root. This information was then analyzed to infer the presence of common software engineering practices.

Specifically, binary indicators were constructed to capture whether a repository contains:

- A README file (documentation)
- A license file (legal clarity)
- A dedicated test directory (testing practices)
- A continuous integration configuration (automation), detected via the presence of a `.github` directory

Each indicator was encoded as a binary variable, with a value of 1 indicating presence and 0 indicating absence.

These quality metrics were merged with the existing popularity and activity data, resulting in an enriched dataset that combines social popularity signals with observable engineering characteristics.

The final dataset was saved as a new processed file to ensure reproducibility and to support subsequent analysis and visualization.

# 9. Exploratory Analysis: Popularity vs Quality

After enriching the dataset with repository-level quality indicators, we performed an exploratory analysis to investigate the relationship between GitHub popularity (measured by star counts) and software quality.

First, we examined the distribution of each quality metric individually. Boxplots revealed that repositories without tests exhibit a wide range of star counts, including some extremely popular projects, whereas repositories with tests tend to cluster around moderate popularity. Similar patterns were observed for other indicators: READMEs are common across most repositories, licenses are less consistently present, and CI configurations are relatively rare.

To summarize overall quality, a simple quality score was created by summing the four binary indicators for each repository, producing a range from 0 (no indicators) to 4 (all indicators).

Scatterplots and descriptive statistics comparing stars to this quality score showed weak but interpretable patterns: high popularity does not necessarily correlate with higher quality, although repositories with more quality indicators tend to have more consistent, moderate popularity.

These findings highlight a disconnect between social popularity and observable software engineering practices, providing a foundation for subsequent analysis, visualization, and narrative storytelling.

All plots and numerical summaries were validated for plausibility and consistency, and the enriched dataset was saved to ensure reproducibility.

The exploratory analysis revealed several noteworthy patterns regarding the relationship between GitHub popularity and observable software quality. Repositories without tests exhibit a wide range of star counts, including a few extremely popular projects, whereas repositories with tests tend to cluster around moderate, more consistent popularity levels. Similar trends are observed for other quality indicators: READMEs are almost universally present, licenses are less consistently applied, and CI configurations are relatively rare. To summarize overall quality, a composite quality score was created by summing the four binary indicators, ranging from 0 (no indicators) to 4 (all indicators present). Comparing star counts against this quality score demonstrates a weak positive association: repositories with higher quality scores tend to show more consistent popularity, but extreme star counts are often achieved by repositories with fewer quality practices. These results highlight a disconnect between social popularity and measurable engineering practices, emphasizing that high visibility on GitHub does not necessarily imply higher software quality. All observations were validated for plausibility, and visualizations such as boxplots and scatterplots were created with appropriate scales and labels to ensure clarity and reproducibility.