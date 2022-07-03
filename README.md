# c4-stats

A utility to mine the leaderboard stats for code4rena.com

## Usage

So far there are two commands: `basic` and `by-contest` (with many more planned). Please send through your feature requests as GitHub Issues.

### `basic` command

**Usage**

```
usage: c4-stats basic [-h] warden [warden ...]

Basic statistics

positional arguments:
  warden

options:
  -h, --help  show this help message and exit
```

**Examples**

```
$ ./c4-stats basic cmichel 0xRajeev
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

### `by-contest` command

**Usage**

```
$ ./c4-stats by-contest --help
usage: c4-stats by-contest [-h] warden [warden ...]

Show award for each contest in descending order of award

positional arguments:
  warden

options:
  -h, --help  show this help message and exit
```

**Examples**

```
$ ./c4-stats by-contest 0xRajeev
```

Output
```json
[
  {
    "warden": "0xRajeev",
    "distribution": [
      {
        "contest": "Vader Protocol contest",
        "amount": "27 ETH + 1000 VETH",
        "start": "April 2021",
        "awardUSD": "$40,849.60"
      },
      {
        "contest": "Yield contest",
        "amount": "$100,000 USDC",
        "start": "May 2021",
        "awardUSD": "$24,201.69"
      },
      {
        "contest": "Gro Protocol contest",
        "amount": "$100,000 USDC",
        "start": "July 2021",
        "awardUSD": "$23,682.33"
... etc ...
```

## Platforms

### Linux and Mac OS X

Should just work on Linux and Mac OS X if you have `python3` installed.

### Windows

You may need the [Python Launcher for Windows](https://www.infoworld.com/article/3617292/how-to-use-pythons-py-launcher-for-windows.html).