# c4-stats

A utility to mine the leaderboard stats for code4rena.com

## Usage

So far there are two commands: `basic` and `by-contest` (with many more planned). Please send through your feature requests as GitHub Issues.

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

### `by-contest` command

**Usage**

```
$ ./c4-stats by-contest --help
usage: c4-stats by-contest [-h] warden [warden ...]

Show award for each contest in descending order of award

positional arguments:
  warden
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