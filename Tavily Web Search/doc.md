# Tavily Web Search Action Guide

This document provides instructions on how to use the Tavily Web Search action and how to configure the action's configuration file.

## 1. Obtaining a Tavily API Key (If Needed)

The provided configuration already includes a development API key.  However, for production use or higher usage limits, you may need to obtain your own Tavily API key.  Visit the Tavily website for more information on pricing and obtaining an API key: [https://tavily.com/](https://tavily.com/)

## 2. Configuring the Action's Configuration File

Here's how to configure the `config.json` file for the Tavily Web Search action. We'll use a function code approach for maximum flexibility:

```json
{
    "actionName": "Tavily Web Search",
    "iconUrl": "https://media.licdn.com/dms/image/v2/D4D0BAQEBo8xFnt9JVA/company-logo_200_200/company-logo_200_200/0/1727983378239/tavily_logo?e=2147483647&v=beta&t=g5QE2PczK2BZP5DcgKmIQGfEpoRoCKfKFgO7fHJnTxg",
    "overview": "Search real-time data on the web using Tavily",
    "typeAction": "code",
    "geminiApiFunctionSpec": {
        "name": "tavily_web_search",
        "description": "Search the web in real-time using Tavily API",
        "parameters": {
            "type": "object",
            "properties": {
                "query": {
                    "type": "string",
                    "description": "The search query to look up on the web"
                },
                "search_depth": {
                    "type": "string",
                    "enum": [
                        "basic",
                        "advanced"
                    ],
                    "description": "The depth of search, higher values return more results"
                },
                "topic": {
                    "type": "string",
                    "enum": [
                        "general",
                        "news",
                        "finance"
                    ],
                    "description": "The topic of search, a general topic will perform a broad search, while a new topic will use our New agent to find tailored information"
                },
                "include_answers": {
                    "type": "string",
                    "enum": [
                        "basic",
                        "advanced"
                    ],
                    "description": "Whether to include direct answers from sources"
                },
                "max_results": {
                    "type": "number",
                    "description": "The maximum number of search result to return"
                }
            },
            "required": [
                "query"
            ]
        }
    },
    "actionHandle": {
        "code": {
            "function": "async function tavily_web_search({ params, envi, prev }) {\n    try {\n        const query = params.query;\n        const search_depth = params.search_depth || \"basic\"; \n        const max_results = params.max_results || 5;\n        const include_answers = params.include_answers ||\"basic\";\n        const apiKey = envi.tavilyApiKey;\n\n        const url = 'https://api.tavily.com/search';\n        const headers = {\n            'Content-Type': 'application/json',\n            'Authorization': `Bearer ${apiKey}`\n        };\n\n        const body = JSON.stringify({\n            query: query,\n            search_depth: search_depth,\n            include_answers: include_answers\n        });\n\n        const response = await fetch(url, {\n            method: 'POST',\n            headers: headers,\n            body: body\n        });\n\n        const data = await response.json();\n\n        if (!response.ok) {\n            throw new Error(data.error || 'Tavily API Error');\n        }\n\n        if (!data || !data.results || data.results.length === 0) {\n            return \"<p>No results found.</p>\";\n        }\n\n        let html = \"<ul>\";\n        data.results.forEach(result => {\n            html += `<li>\n                 <a href=\"${result.url}\" target=\"_blank\">${result.title}</a>\n                 <p>${result.content || ''}</p>\n               </li>`;\n        });\n        html += \"</ul>\";\n\n        return html;\n\n    } catch (error) {\n        console.error('Tavily Web Search Error:', error);\n        return `<p>Error: ${error.message}</p>`; \n    }\n}\n"
        }
    },
    "envVariant": {
        "tavilyApiKey": "<ENTER API KEY>"
    }
}
```

**Configuration Steps:**

1.  **`actionName`:** Keep `"Tavily Web Search"` or modify it as needed.
2.  **`iconUrl`:** You can change the icon URL if desired.
3.  **`envVariant`:**
    *   **`tavilyApiKey`:**  This currently contains a development API key.  **REPLACE THIS WITH YOUR OWN API KEY if you have one, especially for production use!**  You can get a key from [https://tavily.com/](https://tavily.com/).

## 3. Using the Action

To use the Tavily Web Search action, you need to provide the following parameters via the `params` object:

*   **`query` (required):** The search query you want to use.
*   **`search_depth` (optional):** The depth of the search (integer). Higher values return more results. Defaults to 3 if not provided.
*   **`include_answers` (optional):** Whether to include direct answers from sources (boolean). Defaults to `false` if not provided.

The Tavily API Key is retrieved from the `envVariant.tavilyApiKey` during execution, so you don't need to pass it directly when calling the action.

**Example Usage:**

To search for "latest news on AI" with the default search depth and without including direct answers:

```json
{
  "action": "tavily_web_search",
  "parameters": {
    "query": "latest news on AI"
  }
}
```

To search for "best restaurants in Hanoi" with a search depth of 5 and including direct answers:

```json
{
  "action": "tavily_web_search",
  "parameters": {
    "query": "best restaurants in Hanoi",
    "search_depth": "basic",
    "include_answers": "basic",
    "max_results": 5
  }
}
```

**Important Notes:**

*   Ensure that the environment where this action is running has access to the `fetch` API for making HTTP requests.
*   **Treat your API key as a secret!** Do not commit it to public repositories or share it inappropriately.
*   Be mindful of Tavily's usage limits and pricing.
