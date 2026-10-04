# Results 2026-09-23

B1 site, 24 tasks (T1 10 / T2 8 / T3 6), temp 0, 1 run per task.

| Cond | Action set |
| --- | --- |
| P | none, infer requests from HTML |
| J | static catalogue (21 ops) |
| J+ | J + preconditions |
| X | MCP, static tool list |
| X+ | MCP, tools filtered by state |
| H | controls in current page |

## Invalid action rate

| Model | P | J | J+ | X | X+ | H |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| random | - | 90.7 | 89.6 | 88.4 | 0 | 0 |
| qwen3.5:2b | 58.1 | 50.0 | 33.3 | 72.8 | 6.1 | 0 |
| qwen3.5:4b | 8.7 | 79.1 | 76.3 | 62.1 | 0 | 0 |
| qwen3.5:9b | 13.2 | 80.9 | 78.1 | 66.3 | 0 | 0 |
| qwen3.8-27b | 0 | 63.5 | 34.1 | 34.1 | 0 | 0 |

## Success rate

| Model | P | J | J+ | X | X+ | H |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| random | - | 16.7 | 29.2 | 20.8 | 37.5 | 37.5 |
| qwen3.5:2b | 62.5 | 33.3 | 20.8 | 62.5 | 66.7 | 45.8 |
| qwen3.5:4b | 75.0 | 54.2 | 70.8 | 66.7 | 70.8 | 83.3 |
| qwen3.5:9b | 75.0 | 54.2 | 54.2 | 45.8 | 79.2 | 83.3 |
| qwen3.8-27b | 66.7 | 79.2 | 79.2 | 79.2 | 70.8 | 79.2 |

n = 24 per cell. T2-02, T2-04 and T3 judging have known issues.



## Tokens (k)

| Model | P | J | J+ | X | X+ | H |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| qwen3.5:2b | 167 | 28 | 17 | 344 | 151 | 17 |
| qwen3.5:4b | 22 | 191 | 263 | 313 | 81 | 28 |
| qwen3.5:9b | 36 | 216 | 298 | 249 | 44 | 33 |
| qwen3.8-27b | 21 | 146 | 60 | 127 | 23 | 28 |

## Interface

| | H | Catalogue |
| --- | ---: | ---: |
| Dead controls | 0 / 2865 | 18396 / 20937 (87.9%) |
| Controls per state | 2.87 | 21 |
| Values carried by control | 58.4% | 0% |
| Fields to ask per action | 0.24 | 0.57 |





MCP server -> "elicitation"

human-in-the-loop 

tool <-> http request





