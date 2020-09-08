# DAY81



## 크롤링

### beautifulSoup 

```python
doc = BeautifulSoup("<document><content/>INSERT FOOTER HERE</document", "xml")
footer = BeautifulSoup("<footer>Here's the footer</footer>", "xml")
doc.find(text="INSERT FOOTER HERE").replace_with(footer)
# u'INSERT FOOTER HERE'
print(doc)
# <?xml version="1.0" encoding="utf-8"?>
# <document><content/><footer>Here's the footer</footer></document>
```

```python
url = 'http://api.podty.me/api/v1/share/cast/390937d3e5c758aa6f4005b63542cc83695b4d5e6925fe6a2d4d488d1d05d748/146364'    
parser = feedparser.parse(url)
print(parser.feed['title'])

for i,entity in enumerate(parser.entries):         if i ==3:             break         print('title : %s' % entity.title)         print('link : %s' % entity.enclosures[0].href)         print('description : %s' % entity.description)         print('publish date : %s' % entity.published)         wget.download(entity.enclosures[0].href, './') if __name__ == '__main__':     main()
```

