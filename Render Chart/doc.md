# Render Chart Action Guide

This document provides instructions on how to use the Render Chart action and how to configure the action's configuration file. 
(only for MintMint version > 2)
## 1. Overview

The Render Chart action enables users to generate different types of charts, including bar, line, pie, doughnut, radar, polar area, scatter, and mixed charts. The action is designed for dynamic data visualization.

## 2. Configuring the Action's Configuration File

Here's how to configure the `config.json` file for the Render Chart action:

```json
{
    "actionName": "Render Chart",
    "iconUrl": "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRehhGKP0Zn-R-3IJya9iRP0Y3peQRh_Zob5w&s",
    "overview": "Render Chart",
    "typeAction": "code",
    "geminiApiFunctionSpec": {
        "name": "render_chart",
        "description": "Render Chart for data",
        "parameters": {
            "type": "object",
            "properties": {
                "type": {
                    "type": "string",
                    "description": "Type of chart (bar, line, pie, doughnut, radar, polarArea, scatter, mixed)"
                },
                "labels": {
                    "type": "array",
                    "description": "Labels for the chart",
                    "items": {
                        "type": "string"
                    }
                },
                "datasets": {
                    "type": "array",
                    "description": "Array of datasets for the chart",
                    "items": {
                        "type": "object",
                        "properties": {
                            "label": {
                                "type": "string",
                                "description": "Dataset label"
                            },
                            "data": {
                                "type": "array",
                                "items": {
                                    "type": "number"
                                }
                            },
                            "backgroundColor": {
                                "type": "string",
                                "description": "Background color"
                            },
                            "borderColor": {
                                "type": "string",
                                "description": "Border color"
                            },
                            "borderWidth": {
                                "type": "number",
                                "description": "Border width"
                            },
                            "type": {
                                "type": "string",
                                "description": "Optional: type of dataset (for mixed charts)"
                            }
                        },
                        "required": [
                            "label",
                            "data"
                        ]
                    }
                },
                "options": {
                    "type": "object",
                    "description": "Chart.js configuration options"
                }
            },
            "required": [
                "type",
                "labels",
                "datasets"
            ]
        }
    },
    "actionHandle": {
        "code": {
            "function": "async function render_chart({ params }) {\n    try {\n        return JSON.stringify({ type: params.type, labels: params.labels, datasets: params.datasets, options: params.options });\n    } catch (error) {\n        console.error('Render Chart Error:', error);\n        return `<p>Error: ${error.message}</p>`;\n    }\n}"
        }
    }
}
```

**Configuration Steps:**

1. **`actionName`**: Keep `"Render Chart"` or modify it as needed.
2. **`iconUrl`**: Change the icon URL if desired.
3. **`type`**: Define the chart type (bar, line, pie, etc.).
4. **`labels`**: Specify labels for the chart.
5. **`datasets`**:
    - Define multiple datasets with properties like `label`, `data`, `backgroundColor`, `borderColor`, and `borderWidth`.
6. **`options`**: Specify additional Chart.js configuration options.

## 3. Using the Action

To use the Render Chart action, provide the following parameters via the `params` object:

- **`type` (required)**: The type of chart to render.
- **`labels` (required)**: Labels for the x-axis.
- **`datasets` (required)**: Array of dataset objects.
- **`options` (optional)**: Additional Chart.js configuration.

### Example Usage

To generate a bar chart:

```json
{
  "action": "render_chart",
  "parameters": {
    "type": "bar",
    "labels": ["January", "February", "March"],
    "datasets": [
      {
        "label": "Sales",
        "data": [100, 200, 300],
        "backgroundColor": "blue"
      }
    ]
  }
}
```

To create a mixed chart:

```json
{
  "action": "render_chart",
  "parameters": {
    "type": "mixed",
    "labels": ["Q1", "Q2", "Q3"],
    "datasets": [
      {
        "label": "Revenue",
        "data": [5000, 7000, 8000],
        "type": "bar",
        "backgroundColor": "green"
      },
      {
        "label": "Profit Margin",
        "data": [20, 25, 30],
        "type": "line",
        "borderColor": "red"
      }
    ]
  }
}
```

## 4. Important Notes

- Ensure that your environment supports JavaScript for rendering charts.
- Be mindful of the data structure required by Chart.js.

This guide provides a complete reference for configuring and using the Render Chart action effectively.
