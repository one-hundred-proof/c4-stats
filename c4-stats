#!/usr/bin/env python3

import sys
import subprocess
import csv
import urllib.request
from io import StringIO
import json
import argparse
import dateutil.parser
import datetime

CODE_ARENA="https://raw.githubusercontent.com/code-423n4/code423n4.com/main/_data"
FINDINGS_URL=f"https://code4rena.com/community-resources/findings.csv"
CONTESTS_URL=f"{CODE_ARENA}/contests/contests.csv"
OLD_CONTESTS_URL="https://raw.githubusercontent.com/code-423n4/code423n4.com/74913b14b93923341b62f4e3df2ee475e7bf52f1/_data/contests/contests.csv"

parser = argparse.ArgumentParser(prog="c4-stats", formatter_class=argparse.RawTextHelpFormatter)
subp = parser.add_subparsers(dest="command") # dest="command" means that we see which command was parsed

statsp = subp.add_parser("basic", description="Basic statistics")
statsp.add_argument('warden',type=str, nargs='+')

byContestP = subp.add_parser("by-contest", description="Show award for each contest in descending order of award")
byContestP.add_argument('warden', type=str, nargs='+')
byContestP.add_argument('-b', '--sort-by', nargs='?', help="sort by [awardUSD (default), month, start_time, end_time]")
byContestP.add_argument('-s', '--sort-dir', nargs='?', help="sort by [d/descending (default), a/ascending]")

listContestsP = subp.add_parser("list-contests", description="List contests and their contest IDs")

contestP = subp.add_parser("contest", description="Show awards for contest with a particular ID")
contestP.add_argument('id', type=int)
contestP.add_argument('-f', '--csv', nargs='?', help="Load results from CSV file or URL")

ginip = subp.add_parser("gini",
  description='Calculate the Gini coefficient for the entire competition\n' +
              '0.0 is complete equality of result. 1.0 is all awards going to one person\n'+
              'See: https://en.wikipedia.org/wiki/Gini_coefficient',
              formatter_class=argparse.RawTextHelpFormatter)

giniWardens = subp.add_parser("gini-wardens",
  description='Calculate the Gini coefficient for one or more wardens where each contest\n'+
              'is treated as an individual. 0.0 means the warden got the same award for each \n'+
              '1.0 means all awards came from one contest.\n'+
              'See: https://en.wikipedia.org/wiki/Gini_coefficient',
  formatter_class=argparse.RawTextHelpFormatter)
giniWardens.add_argument('warden', type=str, nargs='+')

ns = parser.parse_args(sys.argv[1:])

#
# Functions
#
def get_records(csvr):
  rs = []
  headers = next(csvr, None)
  for row in csvr:
    r = {}
    for i in range(0, len(headers)):
      r[headers[i]] = row[i]
    rs.append(r)
  return rs

def get_records_from_url(url):
  response = urllib.request.urlopen(url)
  csv_data = response.read()
  file = StringIO(bytes.decode(csv_data, "UTF-8"))
  csvr = csv.reader(file)
  return get_records(csvr)

def get_records_from_file(f):
  with open(f, newline='') as csv_file:
    csvr = csv.reader(csv_file)
    return get_records(csvr)

findings_records = get_records_from_url(FINDINGS_URL)

def mk_missing_contest_record(i):
  return { 'contestid': f'{i}', 'title': f'Unknown contest {i}', 'sponsor': 'Unknown', 'start_time': '1970-01-01', 'end_time': '1970-01-01', 'amount': "$0", 'repo' : 'Unknown', 'findingsRepo': 'Unknown', 'hide': 'False', 'league': 'Unknown' }

# It's not strictly necessary that CONTESTS_URL exists
contest_records = []
try:
  contest_records=get_records_from_url(CONTESTS_URL)
except:
  try:
    contest_records=get_records_from_url(OLD_CONTESTS_URL)
  except:
    for i in range(0,1000):
      contest_records.append(mk_missing_contest_record(i))

def make_contest_hash(rs):
  result = {}
  for r in rs:
    result[r['contestid']] = r
  return result

contest_hash = make_contest_hash(contest_records)

def ppUSD(n):
  return '${:0,.2f}'.format(round(n, 2))

def ppMonth(epochTime):
  dt = datetime.datetime.fromtimestamp(epochTime)
  return dt.strftime("%B %Y")

def ppTime(epochTime):
  dt = datetime.datetime.fromtimestamp(epochTime)
  return dt.isoformat();

def basic_stats():
  stats = { }
  lastContest = 0
  for r in findings_records:
    lastContest = r["contest"]
    if r["handle"]:
      handle = r["handle"]
      if not handle in stats:
        stats[handle] = { "handle": handle, "contests": 0, "highs": 0, "mediums": 0, "qaReports": 0, "gasReports": 0, "submissions": 0, "total": 0, "lastContest": 0 }
      if lastContest != stats[handle]["lastContest"]:
        stats[handle]["contests"] += 1
        stats[handle]["lastContest"] = lastContest
      if r["finding"][0:1] == "H":
        stats[handle]["highs"] += 1
      if r["finding"][0:1] == "M":
        stats[handle]["mediums"] += 1
      if r["finding"][0:1] == "Q":
        stats[handle]["qaReports"] += 1
      if r["finding"][0:1] == "G":
        stats[handle]["gasReports"] += 1
      stats[handle]["submissions"] += 1
      stats[handle]["total"] += float(r["awardUSD"])

  for h in stats.keys():
    del stats[h]["lastContest"]
    stats[h]["averagePerContest"] = ppUSD(stats[h]["total"] / stats[h]["contests"])
    stats[h]["averagePerSubmission"] = ppUSD(stats[h]["total"] / stats[h]["submissions"])
    stats[h]["total"] = ppUSD(stats[h]["total"])


  # Collate results
  results = []
  for warden in ns.warden:
    if warden in stats:
      results.append(stats[warden])
    else:
      results.append({ "error" : f"Warden '{warden}' has not competed" })
  return results

def listContests():
  return contest_records

def toEpoch(s):
  return int(dateutil.parser.parse(s).strftime("%s"))

def byContest(ns):
  results = []
  sort_by='awardUSD'
  sort_mult=-1 # -1 for descending, 1 for ascending
  if ns.sort_by != None:
    validOpts = ['awardUSD', 'month', 'start_time', 'end_time']
    if ns.sort_by in validOpts:
      sort_by = ns.sort_by
    else:
      print(f'-b/--sort_by is not one of {validOpts}')
      exit(1)
  if ns.sort_dir != None:
    validOpts = ['a', 'asc', 'ascending', 'd', 'desc', 'descending']
    if ns.sort_dir in validOpts:
      sort_mult = 1 if ns.sort_dir[0:1] == 'a' else -1
    else:
      print(f'--s/--sort-dir is not one of {validOpts}')
      exit(1)
  for warden in ns.warden:
    stats = []
    h = {}
    for r in findings_records:
      if r['handle'] and r['handle'] == warden:
        award = float(r['awardUSD'])
        if not r['contest'] in h:
          if not r['contest'] in contest_hash:
            c = mk_missing_contest_record(r['contest'])
          else:
            c = contest_hash[r['contest']]
          h[r['contest']] = { 'contest' : c['title'],
                              'amount': c['amount'],
                              'month': toEpoch(c['start_time']),
                              'start_time': toEpoch(c['start_time']),
                              'end_time': toEpoch(c['end_time']),
                              'awardUSD' : 0.0,

                            }
        h[r['contest']]['awardUSD'] += award
    for k in h:
      stats.append(h[k])
    stats.sort(key=lambda r: sort_mult * r[sort_by])
    for s in stats:
      s['awardUSD'] = ppUSD(s['awardUSD'])
      s['month'] = ppMonth(s['month'])
      s['start_time'] = ppTime(s['start_time'])
      s['end_time'] = ppTime(s['end_time'])
    results.append({ 'handle': warden, 'distribution': stats} if len(stats) > 0 else { 'error': f'Warden {warden} does not exist'})
  return results


def contestResults(ns):
    global findings_records
    global contest_hash
    if ns.csv != None:
      if ns.csv[0:4] == 'http':
        findings_records = get_records_from_url(ns.csv)
      else:
        findings_records = get_records_from_file(ns.csv)
      contest_hash = {}
    h = {}
    contest_id = str(ns.id)
    for r in findings_records:
      if r['contest'] == contest_id:
        handle = r['handle']
        if not handle in h:
          h[handle] = { 'handle' : handle, 'awardUSD': 0.0 }
        h[handle]['awardUSD'] += float(r['awardUSD'])
    awards = []
    for k in h:
      awards.append(h[k])
    awards.sort(key=lambda r: -r['awardUSD'])
    place = 0
    for r in awards:
      place += 1
      r['awardUSD'] = ppUSD(r['awardUSD'])
      r['place'] = place
    if contest_id in contest_hash:
      result = contest_hash[contest_id]
    else:
      result = { 'contestid': contest_id, 'title': 'Unknown contest'}
    result['num_contestants'] = place
    result['awards'] = awards
    return result

#
# Calculates the Gini coefficient (https://en.wikipedia.org/wiki/Gini_coefficient)
#
# Takes
# - h, a dictionary mapping keys to sub-records.
# - amountKey, The key of the sub-record that returns a float amount
#
# Each record is regarded as an individual when calculating the Gini coefficient
#
def calculate_gini(h, amountKey):
  total = 0
  n = 0
  for i in h:
    total += h[i][amountKey]
    n += 1
  mean = total/n

  gini_numerator = 0.0
  # Now iterate through each warden and calculate the Gini coefficient
  for i in h:
    for j in h:
      gini_numerator += abs(h[i][amountKey] - h[j][amountKey])
  return gini_numerator / (2*n*n*mean)


#
# Calculates the gini coefficient of the entire competition since the beginning
#
def gini():
  h = {}
  for r in findings_records:
    handle = r['handle']
    if not handle in h:
      h[handle] = { 'handle': handle, 'awardUSD' : 0 }
    h[handle]['awardUSD'] += float(r['awardUSD'])
  # Now work out the mean
  total = 0.0
  n = 0
  result = calculate_gini(h, 'awardUSD')
  return { "gini" : result }

#
# Calculates the gini coefficient for a warden by treating each competition as a separate "person".
# Gives a measure of how unequally the contests pay-off for a specific warden.
#
def giniForWardens(handles):
  results = []
  for handle in handles:
    h = {}
    warden_found=False
    for r in findings_records:
      if r['handle'] == handle:
        warden_found=True
        cid  = r['contest']
        if not cid in h:
          h[cid] = { 'awardUSD': 0.0 }
        h[cid]['awardUSD'] += float(r['awardUSD'])
    if not warden_found:
      results.append({ 'error' : f'Warden {handle} does not exist'})
    else:
      gini = calculate_gini(h, 'awardUSD')
      results.append({ f"gini({handle})" : gini })
  return results

## Prints the records as valid JSON

if ns.command == "basic":
  result = basic_stats()
elif ns.command == "by-contest":
  result = byContest(ns)
elif ns.command == "gini":
  result = gini()
elif ns.command == 'gini-wardens':
  result = giniForWardens(ns.warden)
elif ns.command == 'list-contests':
  result = listContests()
elif ns.command == "contest":
  result = contestResults(ns)
else:
  parser.parse_args(["--help"])

print(json.dumps(result, indent=2))
