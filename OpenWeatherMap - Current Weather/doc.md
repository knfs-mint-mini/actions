# OpenWeatherMap - Current Weather Action Guide

This document provides instructions on how to use the OpenWeatherMap - Current Weather action, how to obtain an API key from OpenWeatherMap, and how to configure the action's configuration file.

## 1. Obtaining an OpenWeatherMap API Key

To use the OpenWeatherMap action, you need an API key. Follow these steps to obtain one:

1.  **Visit the OpenWeatherMap website:** Go to [https://home.openweathermap.org/](https://home.openweathermap.org/).
2.  **Sign Up:** Click on the "Sign Up" button and fill in the required information to create an account.
3.  **Verify Your Email:** After signing up, you will receive a verification email. Click the link in the email to verify your account.
4.  **Log In:** Log in to your OpenWeatherMap account.
5.  **Access API Keys:** Go to the API keys page: [https://home.openweathermap.org/api_keys](https://home.openweathermap.org/api_keys).
6.  **Create a New API Key:** Click the "Create" button to generate a new API key.
7.  **Name Your API Key:** Enter a descriptive name for your API key (e.g., "My Weather App").
8.  **Save Your API Key:** Once created, the API key will be displayed. **Copy and store this API key in a safe place. You will need it to configure the action.**

## 2. Configuring the Action's Configuration File

Here's how to configure the `config.json` file for the OpenWeatherMap - Current Weather action:

```json
{
    "actionName": "OpenWeatherMap - Current Weather",
    "iconUrl": "https://openweathermap.org/img/wn/01d.png",
    "overview": "Get current weather data from OpenWeatherMap",
    "typeAction": "code",
    "geminiApiFunctionSpec": {
        "name": "get_current_weather",
        "description": "Get the current weather data for a specific city from OpenWeatherMap",
        "parameters": {
            "type": "object",
            "properties": {
                "city": {
                    "type": "string",
                    "description": "The city to get weather data for"
                },
                "units": {
                    "type": "string",
                    "description": "The units to use for temperature (e.g., metric for Celsius, imperial for Fahrenheit)"
                }
            },
            "required": [
                "city"
            ]
        }
    },
    "actionHandle": {
        "code": {
            "function": "async function get_current_weather({ params, envi, prev }) {\n  try {\n    const city = params.city;\n    const units = params.units || 'metric'; // Default to metric\n    const appid = envi.appid; // Get appid from envi\n\n    const url = `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${appid}&units=${units}`;\n\n    const response = await fetch(url);\n    const data = await response.json();\n\n    if (response.ok) {\n      return data;\n    } else {\n      throw new Error(data.message);\n    }\n  } catch (error) {\n    console.error(\"OpenWeatherMap Error:\", error);\n    return { error: error.message };\n  }\n}\n"
        }
    },
    "envVariant": {
        "appid": "YOUR_API_KEY"
    }
}
```

**Configuration Steps:**

1.  **`actionName`:**  Keep `"OpenWeatherMap - Current Weather"` or modify it as needed.
2.  **`iconUrl`:**  You can change the icon URL if desired.
3.  **`envVariant`:**
    *   Replace `"YOUR_API_KEY"` with the API key you obtained from OpenWeatherMap.  **This is crucial for the action to work.**

## 3. Using the Action

To use the OpenWeatherMap - Current Weather action, you need to provide the following parameters:

*   **`city` (required):** The name of the city for which you want to retrieve weather data.  Pass this via the `params` object.
*   **`units` (optional):** The units for temperature.  Pass this via the `params` object.  Valid values are:
    *   `"metric"`:  Celsius (default)
    *   `"imperial"`: Fahrenheit
*   **`appid`:**  The API key is automatically retrieved from the `envVariant` during execution, so you don't need to pass it directly when calling the action.  Make sure you've set it correctly in the `envVariant` section of the config file.

**Example Usage:**

To get the current weather for Ho Chi Minh City in Celsius:

```json
{
  "action": "get_current_weather",
  "parameters": {
    "city": "Ho Chi Minh City",
    "units": "metric"
  }
}
```

To get the current weather for New York City in Fahrenheit:

```json
{
  "action": "get_current_weather",
  "parameters": {
    "city": "New York City",
    "units": "imperial"
  }
}
```

**Important Notes:**

*   Ensure that the environment where this action is running has access to the `fetch` API for making HTTP requests.
*   Keep your API key secure. Do not share it publicly.
