# Multi-Agent-AI-Procurement

**Multi-Agent-AI-Procurement** is an AI-driven procurement system built using the CrewAI framework. It automates the process of searching, scraping, and comparing prices for office coffee machines across e-commerce websites in Egypt (Amazon.eg, Jumia.com.eg, and Noon.com/egypt-en). The system leverages a multi-agent architecture to generate search queries, fetch search results, extract product details, and produce a professional procurement report in HTML format. This project is designed for Rankyx, a company focused on AI solutions for search and recommendation systems.

![6124wLRNZmL _AC_SX679_](https://github.com/user-attachments/assets/1225663b-88b2-44d6-9ddb-cdfaab615222)


## Project Overview

The project consists of four specialized AI agents, each responsible for a distinct stage of the procurement process. These agents work sequentially to deliver a comprehensive procurement report. The system is implemented in Python, using CrewAI for agent orchestration, Tavily for search, ScrapeGraphAI for web scraping, and Gemini LLM for natural language processing.

### Key Components

The project is divided into four main agents and their corresponding tasks, as described below:

#### 1. Search Queries Recommendation Agent
- **Purpose**: Generates a list of targeted search queries to find coffee machines on specified e-commerce websites.
- **Task**: Produces a JSON object containing up to three search queries (e.g., "DeLonghi Dedica EC685 espresso machine on amazon.eg") based on inputs like product name, target websites, country (Egypt), and language (English).
- **Key Features**:
  - Uses the Gemini LLM to generate varied queries that include specific brands, types, or technologies.
  - Outputs queries in a structured JSON format, saved to `step1_suggested_search_queries.json`.
  - Includes a 90-second delay callback to manage API rate limits.
- **Implementation Details**:
  - Defined using the `suggestedSearchQureis` Pydantic model for structured output.
  - Configured to ensure queries are specific to the provided websites and country.

#### 2. Search Engine Agent
- **Purpose**: Searches for products using the generated queries and filters results to identify relevant e-commerce product pages.
- **Task**: Collects search results from multiple queries, ignoring suspicious links, low-confidence results (below a threshold, e.g., 0.1), and non-target websites. Selects up to two relevant URLs per query.
- **Key Features**:
  - Utilizes the Tavily API via a custom `search_engine_tool` to perform web searches.
  - Outputs a JSON object with search results (title, URL, content, score, and query), saved to `Step_2_Search_results.json`.
  - Includes a 90-second delay callback to avoid rate limit issues.
- **Implementation Details**:
  - Uses the `SinglSearchResult` and `AllSearchResults` Pydantic models for structured output.
  - Ensures results are from actual e-commerce product pages ready for purchase.

#### 3. Web Scraping Agent
- **Purpose**: Extracts detailed product information from the e-commerce URLs identified by the Search Engine Agent.
- **Task**: Scrapes product details (e.g., title, price, description, specifications, rating) from multiple URLs and selects the top 10 products based on relevance and value.
- **Key Features**:
  - Employs the ScrapeGraphAI API via a custom `web_scrapping_tool` to extract structured data.
  - Outputs a JSON object containing product details, saved to `Step_3_extracted_products.json`.
  - Includes fields like current price, original price, discount percentage, and agent recommendation notes.
  - Includes a 90-second delay callback to manage API rate limits.
- **Implementation Details**:
  - Defined using the `singleExtractedProduct` and `AllExtractedProducts` Pydantic models for structured output.
  - Limits specifications to 1–5 key attributes for comparison.

#### 4. Procurement Report Author Agent
- **Purpose**: Generates a professional HTML procurement report summarizing the findings and recommendations.
- **Task**: Creates an HTML page using the Bootstrap CSS framework, structured with eight sections: Executive Summary, Introduction, Methodology, Findings, Analysis, Recommendations, Conclusion, and Appendices.
- **Key Features**:
  - Incorporates search results and product details, including prices, in a comparative table.
  - Uses Rankyx’s company context to tailor the report.
  - Outputs the report to `Step_4_Procurment_report.html`.
  - Includes a 90-second delay callback for consistency.
- **Implementation Details**:
  - Relies on the Gemini LLM to generate well-formatted HTML content.
  - Ensures the report is visually appealing and includes all required sections.

#### Crew Orchestration
- The agents are orchestrated using a `Crew` object from CrewAI, configured for sequential task execution.
- The crew integrates a `StringKnowledgeSource` containing Rankyx’s company context to inform agent decisions.
- Inputs (e.g., product name, website list, country) are passed to the crew via the `kickoff` method, triggering the entire workflow.

## Prerequisites

To run this project, you need the following:

- **Python**: Version 3.8 or higher.
- **API Keys**:
  - Tavily API key for search functionality.
  - ScrapeGraphAI API key for web scraping.
  - Google Gemini API key for the LLM.
- **Dependencies**: Install the required Python packages listed in the setup section below.

## Setup Instructions

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/Shrouk-Adel/Multi-Agent-AI-Procurement.git
   cd Multi-Agent-AI-Procurement
   ```

2. **Install Dependencies**:
   Install the required Python packages using pip:
   ```bash
   pip install crewai crewai-tools tavily-python scrapegraph-py litellm pydantic google-colab
   ```

3. **Set Up API Keys**:
   - Store your API keys in a secure location (e.g., environment variables or Google Colab’s `userdata`).
   - Update the code to load keys appropriately, replacing `userdata.get` calls with your preferred method (e.g., `os.getenv`).

4. **Prepare the Output Directory**:
   The project creates an `ai-agent-output` directory to store JSON and HTML outputs. Ensure write permissions in the project directory.

## Usage

1. **Run the Notebook**:
   - Open `Multi_Agents_Crewai.ipynb` in Jupyter Notebook or Google Colab.
   - Execute the cells sequentially to install dependencies, define agents, and run the crew.

2. **Customize Inputs**:
   Modify the `inputs` dictionary in the `rankyx_crew.kickoff` call to change the product, websites, or other parameters. Example:
   ```python
   crew_results = rankyx_crew.kickoff(
       inputs={
           "product_name": "coffee machine for office",
           "website_list": ["www.amazon.eg", "www.jumia.com.eg", "www.noon.com/egypt-en"],
           "no_keywords": 3,
           "country_name": "Egypt",
           "language_name": "English",
           "score_th": 0.10,
       }
   )
   ```

3. **View Outputs**:
   - Check the `ai-agent-output` directory for the following files:
     - `step1_suggested_search_queries.json`: Suggested search queries.
     - `Step_2_Search_results.json`: Search engine results.
     - `Step_3_extracted_products.json`: Scraped product details.
     - `Step_4_Procurment_report.html`: Final procurement report.

## Project Structure

```
Multi-Agent-AI-Procurement/
├── Multi_Agents_Crewai.ipynb  # Main notebook with project code
├── ai-agent-output/           # Directory for output files
│   ├── step1_suggested_search_queries.json
│   ├── Step_2_Search_results.json
│   ├── Step_3_extracted_products.json
│   ├── Step_4_Procurment_report.html
├── README.md                 # This file
```

## Notes

- **API Rate Limits**: The 90-second delay callback (`add_delay_callback`) is included to manage API rate limits for Tavily and ScrapeGraphAI. Adjust the delay (`time.sleep(90)`) based on your API plan if needed.
- **Error Handling**: The ScrapeGraphAI client may throw errors if the `scrape` method is not supported. Verify the correct method name in the ScrapeGraphAI documentation.
- **Scalability**: The project can be extended to support additional products, websites, or countries by modifying the input parameters and agent configurations.

 
