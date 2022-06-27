# c4-stats

A utility to mine the leaderboard stats for code4rena.com

## Usage

```
$ ./c4-stats cmichel 0xRajeev

[{"handle": "cmichel", "contests": 93, "highs": 181, "mediums": 223, "qaReports": 12, "gasReports": 116, "total": 1148956.13, "submissions": 946, "averagePerContest": 12354.37, "averagePerSubmission": 1214.54},{"handle": "0xRajeev", "contests": 26, "highs": 52, "mediums": 72, "qaReports": 0, "gasReports": 163, "total": 290696.7, "submissions": 628, "averagePerContest": 11180.64, "averagePerSubmission": 462.89}]
```

To pretty print the output just pipe into `jq`

```
$ ./c4-stats cmichel 0xRajeev | jq
```

Output

```json
[
  {
    "handle": "cmichel",
    "contests": 93,
    "highs": 181,
    "mediums": 223,
    "qaReports": 12,
    "gasReports": 116,
    "total": "$1,148,956.13",
    "submissions": 946,
    "averagePerContest": "$12,354.37",
    "averagePerSubmission": "$1,214.54"
  },
  {
    "handle": "0xRajeev",
    "contests": 26,
    "highs": 52,
    "mediums": 72,
    "qaReports": 0,
    "gasReports": 163,
    "total": "$290,696.70",
    "submissions": 628,
    "averagePerContest": "$11,180.64",
    "averagePerSubmission": "$462.89"
  }
]
```
