# c4-stats

A utility to mine the leaderboard stats for code4rena.com

## Installation

```
$ pip -r requirements.txt
```

## Usage

Here are the current commands (with many more planned):
- `basic`
- `list-contests`
- `contest`
- `by-contest`
- `gini`
- `gini-wardens`

If you'd like to see more, or tweaks on the current commands, please send through your feature requests as GitHub Issues.

### `basic` command

**Usage**

```
$ ./c4-stats basic --help
usage: c4-stats basic [-h] warden [warden ...]

Basic statistics

positional arguments:
  warden
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

### `list-contests` command

**Usage**

```
$ ./c4-stats list-contests --help
usage: c4-stats list-contests [-h]

List contests and their contest IDs

options:
  -h, --help  show this help message and exit
```

**Examples**

```
$ ./c4-stats list-contests
```

```json
[
  {
    "contestid": "1",
    "title": "Slingshot Finance contest",
    "sponsor": "Slingshot Finance",
    "details": "Slingshot is a decentralized trading platform.",
    "start_time": "2021-02-17T00:00:00.000Z",
    "end_time": "2021-02-22T23:59:00.000Z",
    "amount": "$20,000 USDC",
    "repo": "https://github.com/code-423n4/2021-02-slingshot",
    "findingsRepo": "https://github.com/code-423n4/2021-02-slingshot-findings",
    "hide": "False",
    "league": "eth"
  },
  {
    "contestid": "2",
    "title": "ElasticDAO contest",
    "sponsor": "ElasticDAO",
    "details": "ElasticDAO is an experiment in fair governance.",
    "start_time": "2021-02-25T00:00:00.000Z",
    "end_time": "2021-03-03T23:59:00.000Z",
    "amount": "22 ETH",
    "repo": "https://github.com/code-423n4/2021-03-elasticdao",
    "findingsRepo": "https://github.com/code-423n4/2021-03-elasticdao-findings",
    "hide": "False",
    "league": "eth"
  },
  "... output truncated ..."
```

### `contest` command

**Usage**

```
usage: c4-stats contest [-h] [-f [CSV]] id

Show awards for contest with a particular ID

positional arguments:
  id

options:
  -h, --help            show this help message and exit
  -f [CSV], --csv [CSV]
                        Load results from CSV file or URL
```

**Examples**

List results for contest 50

```
$ ./c4-stats contest 50
```

```json
{
  "contestid": "50",
  "title": "FairSide contest",
  "sponsor": "FairSide",
  "details": "Decentralized Cost Sharing Network.",
  "start_time": "2021-11-09T00:00:00.000Z",
  "end_time": "2021-11-11T23:59:00.000Z",
  "amount": "$30,000 worth of ETH",
  "repo": "https://github.com/code-423n4/2021-11-fairside",
  "findingsRepo": "https://github.com/code-423n4/2021-11-fairside-findings",
  "hide": "False",
  "league": "eth",
  "num_contestants": 17,
  "awards": [
    {
      "handle": "cmichel",
      "awardUSD": "$9,818.99",
      "place": 1
    },
    {
      "handle": "leastwood",
      "awardUSD": "$5,960.99",
      "place": 2
    },
    {
      "handle": "WatchPug",
      "awardUSD": "$5,600.54",
      "place": 3
    },
    {
      "handle": "hickuphh3",
      "awardUSD": "$3,653.72",
      "place": 4
    },
    "... output truncated ..."
```

List results for a contest that is in GitHub Pull Request but hasn't yet been posted to the #announcements channel

```
$ ./c4-stats contest -f https://raw.githubusercontent.com/code-423n4/code423n4.com/fc8907b162994f1b7092f4d4243ab76b08e430fe/_data/findings/findings.csv 129
```

```json
{
  "contestid": "129",
  "title": "Unknown contest",
  "num_contestants": 99,
  "awards": [
    {
      "handle": "WatchPug",
      "awardUSD": "$6,358.48",
      "place": 1
    },
    {
      "handle": "cccz",
      "awardUSD": "$3,469.41",
      "place": 2
    },
    {
      "handle": "0x1f8b",
      "awardUSD": "$2,900.21",
      "place": 3
    },
    {
      "handle": "berndartmueller",
      "awardUSD": "$2,844.65",
      "place": 4
    },
    "... output truncated ..."
```

### `by-contest` command

**Usage**

```
$ ./c4-stats by-contest --help
usage: c4-stats by-contest [-h] [-b [SORT_BY]] [-s [SORT_DIR]] warden [warden ...]

Show award for each contest in descending order of award

positional arguments:
  warden

options:
  -h, --help            show this help message and exit
  -b [SORT_BY], --sort-by [SORT_BY]
                        sort by [awardUSD (default), month, start_time, end_time]
  -s [SORT_DIR], --sort-dir [SORT_DIR]
                        sort by [d/descending (default), a/ascending]```
```

**Examples**

```
$ ./c4-stats by-contest 0xRajeev
```

Output
```json
[
  {
    "handle": "0xRajeev",
    "distribution": [
      {
        "contest": "Vader Protocol contest",
        "amount": "27 ETH + 1000 VETH",
        "month": "April 2021",
        "start_time": "2021-04-22T00:00:00",
        "end_time": "2021-04-28T23:59:00",
        "awardUSD": "$40,849.60"
      },
      {
        "contest": "Yield contest",
        "amount": "$100,000 USDC",
        "month": "May 2021",
        "start_time": "2021-05-27T00:00:00",
        "end_time": "2021-06-02T23:59:00",
        "awardUSD": "$24,201.69"
      },
      {
        "contest": "Gro Protocol contest",
        "amount": "$100,000 USDC",
        "month": "July 2021",
        "start_time": "2021-07-01T00:00:00",
        "end_time": "2021-07-07T23:59:00",
        "awardUSD": "$23,682.33"
      },
      "... output truncated ..."
```

### `gini` command

This command measures the Gini coefficient, a measure of equality/inequality of outcome, on the entire awards. See [Wikipedia](https://en.wikipedia.org/wiki/Gini_coefficient) for more detail.


**Usage**

```
$ ./c4-stats gini --help
usage: c4-stats gini [-h]

Calculate the Gini coefficient for the entire competition
0 is complete equality of result, 1 is all awards going to one person
(https://en.wikipedia.org/wiki/Gini_coefficient)
```

**Examples**

```
$ ./c4-stats gini
```

```json
{
  "gini": 0.8637485784663245
}
```


### `gini-wardens` command

This command is a lot like `gini` except that instead of treating each warden as an individual, it assumes a particular warden, and then calculates the Gini coefficient treating each _competition_ as an individual. Thus, it is a measure of consistency from competition to competition. A score of 0 would mean the warden always received the same award, while a score of 1 would mean they received 0 on all competitions save one.

This command can take multiple wardens as arguments.

**Usage**

```
$ ./c4-stats gini-wardens --help
usage: c4-stats gini-wardens [-h] warden [warden ...]

Calculate the Gini coefficient for one or more wardens where each contest
is treated as an individual. The closer to 0 the more consistent a
warden is.

positional arguments:
  warden
```

**Examples**

```
$ ./c4-stats gini-wardens cmichel 0xRajeev
```

```json
[
  {
    "gini(cmichel)": 0.5353830055383504
  },
  {
    "gini(0xRajeev)": 0.3878875606298723
  }
]
```

## Platforms

### Linux and Mac OS X

Should just work on Linux and Mac OS X if you have `python3` installed.

### Windows

You may need the [Python Launcher for Windows](https://www.infoworld.com/article/3617292/how-to-use-pythons-py-launcher-for-windows.html).