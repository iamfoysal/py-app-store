![build](https://img.shields.io/github/workflow/status/iamfoysal/py-app-store/Build)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](https://github.com/iamfoysal/py-app-store/pulls)
[![PyPI](https://img.shields.io/pypi/v/python-app-store-scraper)](https://pypi.org/project/python-app-store-scraper/)
![downloads](https://img.shields.io/pypi/dm/python-app-store-scraper)
![license](https://img.shields.io/pypi/l/python-app-store-scraper)
![code style](https://img.shields.io/badge/code%20style-black-black)


# Quickstart

Install:
```console
pip3 install python-app-store-scraper
```

Scrape reviews for an app:
```python
from app_store_scraper import AppStore
from pprint import pprint

facebook = AppStore(country="us", app_name="facebook")
facebook.review(how_many=20)

pprint(facebook.reviews)
pprint(facebook.reviews_count)
```

Scrape reviews for a podcast:
```python
from app_store_scraper import Podcast
from pprint import pprint

sysk = Podcast(country="us", app_name="stuff you should know")
sysk.review(how_many=20)

pprint(sysk.reviews)
pprint(sysk.reviews_count)
```


## Instantiation

There are two required and one positional parameters:

- `country` (required)
  - two-letter country code of [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) standard
- `app_name` (required)
  - name of an iOS application to fetch reviews for
  - also used by `search_id()` method to search for `app_id` internally
- `app_id` (positional)
  - can be passed directly
  - or ignored to be obtained by `search_id` method internally

Once instantiated, the object can be examined:
```pycon
>>> facebook
AppStore(country='us', app_name='facebook', app_id=284882215)
```
```pycon
>>> print(app)
     Country | us
        Name | facebook
          ID | 284882215
         URL | https://apps.apple.com/us/app/facebook/id284882215
Review count | 0
```

Other optional parameters are:

- `log_format`
  - passed directly to `logging.basicConfig(format=log_format)`
  - default is `"%(asctime)s [%(levelname)s] %(name)s - %(message)s"`
- `log_level`
  - passed directly to `logging.basicConfig(level=log_level)`
  - default is `"INFO"`
- `log_interval`
  - log is produced every 5 seconds (by default) as a "heartbeat" (useful for a long scraping session)
  - default is `5`


## Fetching Review

The maximum number of reviews fetched per request is 20. To minimise the number of calls, the limit of 20 is hardcoded. This means the `review()` method will always grab more than the `how_many` argument supplied with an increment of 20.

```pycon
>>> facebook.review(how_many=33)
>>> facebook.reviews_count
40
```

If `how_many` is not provided, `review()` will terminate after *all* reviews are fetched.

**NOTE** the review count seen on the landing page differs from the actual number of reviews fetched. This is simply because only *some* users who rated the app also leave reviews.

### Optional Parameters

- `after`
  - a `datetime` object to filter older reviews
- `sleep`
  - an `int` to specify seconds to sleep between each call

## Review Data

The fetched review data are loaded in memory and live inside `reviews` attribute as a list of dict.
```pycon
>>> facebook.reviews
[{'userName': 'someone', 'rating': 5, 'date': datetime.datetime(...
```

Each review dictionary has the following schema:
```python
{
    "date": datetime.datetime,
    "isEdited": bool,
    "rating": int,
    "review": str,
    "title": str,
    "userName": str
 }
```
