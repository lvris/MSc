# Results 

B1 site, 24 tasks (T1 10 / T2 8 / T3 6), temp 0, 1 run per task. Task set v3 (2026-10-21).

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
| random | - | 91.2 | 89.8 | 89.0 | 0 | 0 |
| qwen3.5:2b | 48.9 | 44.4 | 33.3 | 71.1 | 3.5 | 0 |
| qwen3.5:4b | 0 | 72.4 | 72.1 | 52.9 | 0 | 0 |
| qwen3.5:9b | 15.6 | 82.1 | 72.0 | 65.9 | 0 | 0 |
| qwen3.8-27b | 0 | 53.2 | 31.8 | 31.6 | 0 | 0 |

## Success rate

| Model | P | J | J+ | X | X+ | H |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| random | - | 16.7 | 25.0 | 12.5 | 33.3 | 37.5 |
| qwen3.5:2b | 62.5 | 29.2 | 12.5 | 66.7 | 62.5 | 37.5 |
| qwen3.5:4b | 75.0 | 58.3 | 66.7 | 70.8 | 58.3 | 75.0 |
| qwen3.5:9b | 70.8 | 54.2 | 54.2 | 58.3 | 70.8 | 79.2 |
| qwen3.8-27b | 66.7 | 70.8 | 70.8 | 66.7 | 70.8 | 79.2 |

n = 24 per cell.

## T3: stopped clean / stopped after changing state / did not stop

| Model | P | J | J+ | X | X+ | H |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| qwen3.5:2b | 4/2/0 | 5/1/0 | 2/0/4 | 3/0/3 | 1/2/3 | 5/1/0 |
| qwen3.5:4b | 4/2/0 | 1/0/5 | 3/1/2 | 2/1/3 | 1/3/2 | 3/2/1 |
| qwen3.5:9b | 4/1/1 | 1/0/5 | 1/0/5 | 2/1/3 | 4/1/1 | 4/1/1 |
| qwen3.8-27b | 4/2/0 | 2/1/3 | 3/2/1 | 3/3/0 | 4/1/1 | 3/1/2 |

Only "stopped clean" counts as success.

## Tokens (k)

| Model | P | J | J+ | X | X+ | H |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| qwen3.5:2b | 188 | 30 | 14 | 282 | 115 | 12 |
| qwen3.5:4b | 23 | 145 | 240 | 242 | 47 | 31 |
| qwen3.5:9b | 31 | 170 | 258 | 201 | 27 | 27 |
| qwen3.8-27b | 18 | 115 | 78 | 117 | 21 | 31 |

## Interface

| | H | Catalogue |
| --- | ---: | ---: |
| Dead controls | 0 / 2865 | 18396 / 20937 (87.9%) |
| Controls per state | 2.87 | 21 |
| Values carried by control | 58.4% | 0% |
| Fields to ask per action | 0.24 | 0.57 |

# UI view of the same logs

## Dead suggested buttons (%)

| Model | P | J | J+ | X | X+ | H |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| random | - | 88.5 | 87.2 | 86.7 | 0 | 0 |
| qwen3.5:2b | 41.5 | 37.5 | 33.3 | 47.8 | 3.5 | 0 |
| qwen3.5:4b | 0 | 46.2 | 47.3 | 31.8 | 0 | 0 |
| qwen3.5:9b | 10.0 | 53.2 | 41.1 | 46.2 | 0 | 0 |
| qwen3.8-27b | 0 | 39.0 | 25.0 | 29.7 | 0 | 0 |

## Tasks where the user sees at least one dead button (%)

| Model | P | J | J+ | X | X+ | H |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| random | - | 100 | 100 | 100 | 0 | 0 |
| qwen3.5:2b | 45.8 | 12.5 | 8.3 | 54.2 | 12.5 | 0 |
| qwen3.5:4b | 0 | 45.8 | 50.0 | 29.2 | 0 | 0 |
| qwen3.5:9b | 12.5 | 66.7 | 58.3 | 45.8 | 0 | 0 |
| qwen3.8-27b | 0 | 41.7 | 33.3 | 29.2 | 0 | 0 |

## Made-up personal data on the form

| Model | P | J | J+ | X | X+ | H |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| qwen3.5:2b | 4/11 | - | - | 7/13 | 21/35 | 0/5 |
| qwen3.5:4b | 0/5 | 0/7 | 0/15 | 4/16 | 6/15 | 0/15 |
| qwen3.5:9b | 0/4 | 0/6 | 0/10 | 7/9 | 5/7 | 0/7 |
| qwen3.8-27b | - | 0/15 | 0/13 | 1/11 | 0/4 | 0/32 |

Values copied from the goal or from the page are not counted as made up. 

(Same instruction in every condition: leave personal data empty, the user will be asked)

## Fields asked of the user that the site already determines (date, partySize, dish)

| Model | P | J | J+ | X | X+ | H |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| qwen3.5:2b | 5 | 2 | 4 | 0 | 0 | 0 |
| qwen3.5:4b | 4 | 12 | 5 | 0 | 0 | 0 |
| qwen3.5:9b | 6 | 4 | 6 | 0 | 0 | 0 |
| qwen3.8-27b | 0 | 5 | 0 | 0 | 0 | 0 |

X / X+ near 0 because the model fills the values itself, read together with the table above.

## Offered list drawn as the screen, states actually visited

| | J | J+ | X | X+ | H |
| --- | ---: | ---: | ---: | ---: | ---: |
| Dead controls, all models | 86–89% | 86–88% | 85–87% | 0 | 0 |

