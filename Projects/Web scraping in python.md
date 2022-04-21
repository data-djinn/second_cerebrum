[[Python]]
- automate data extraction from online sources
### Use cases
- comparing competitor's prices
- measure satisfaction of customers on social media
- generating potential leads

### Setup
- understand what we want to do
- find data sources to do it
### Acquisition
- read raw data from online sources
- format these data to be useful
### Process data

# Intro to HTML
==HyperText Markup Language: read by web browsers to render & display website content==
```
<html>
  <body>
    <div>
      <p>Hello World!</p>
      <p>Enjoy DataCamp!</p>
    </div>
    <p> Thanks for watching!</p>
  </body>
</hmtl>
```
- "HTML tags" come in nested pairs
  - `<div>`: section of the body
  - `<p>`: paragraphs within the body
- nesting gives rise to a tree structure, like so:
![[Pasted image 20220319195634.png]]

## Attributes
- information within HTML tags can be valuable
- extract link URLs
- easier way to select elements than traversing the entire HTML tree
### Tags
- contain **attributes** which provide special instructions/information for the contents contained within that tag (usually in quoted text)
```
<div id='unique-id' class='some class'>
  <a href='https://www.datacamp.com'>
  this text links to DataCamp!
  </a>
</div>
```
- id attribute should be unique (quick way to 'jump' to this element when scraping
- **class** attribute doesn't need to be unique
- tags don't require id nor class attribute
- tags can belong to **multiple classes**, and 
  - this is done when the class attribute (quoted text) has multpile class names separated by spaces (above div tag would belong to 2 classes: some & class)
- **a** tags are for **hyperlinks**
- **href** attribute tells what link to navigate to
- many allowable tag types, and many allowable attributes that correspond to those tag types

## Xpath notation
- standard, program-friendly syntax to describe where HTML elements are within our programs
`xpath = '/html/body/div[2]'
![[Pasted image 20220320134053.png]]
- span element is not a `div` element, so it is not counted when looking at the div elements
- single `/` used to move forward one generation
- tag-names between slashes give direction to which element(s)
- `[n]` after a tag name tell us which of the selected siblings to choose
- `//`: look forward to **all future generations** (instead of just one generation with `/`)
  - e.g.: `//table'` directs to all `table` elements within the entire HTML code
  - direct to all `table` elements which are descendants of the 2nd `div` child of the `body` element: `/html/body/div[2]//table`
- select elements by their attributes:
`//div[@id='uid']`

### Wildcard character `*`
- indicates that we want to ignore tag type
![[Pasted image 20220320223602.png]]

### `@` represents 'attribute'
- `@class`
- `@id`
- `@href`

e.g.:
- `//p[@class='class-1']`
- `//div[@id='uid']/p[2]`

- can select the class itself by omitting brackets at the end of the xpath
 
### `contains(@attr_name, 'string_expression')`
`//*[contains(@class, 'class-1)]`
- [x] `<p class='class-1'> ... </p>
- [x] `<div class='class-1 class-2'> ... </div>`
- [x] `<p class="class-1 2> ... </p>`
- searches for **all matching substrings**
- compare to regular attribute selector, which only matches elements whose entire class attribute is equal to `'class-1'`

## `scrapy Selector` object
```
from scrapy import Selector

html = '''...'''
sel = Selector(text = html) # sel has now selected the entire html doc
```
- use `sel.xpath()`  method to create new `Selector`s of specific pieces of the HMTL code
  - returns a `SelectorList` of `Selector` objects:
```
sel.xpath('//p')
# outputs a SelectorList:
[<Selector xpath='//p' data='<p>Hello World!</p>'>,
<Selector xpath='//p' data='<p>Enjoy DataCamp!</p>'>]
```
- chain together multiple `xpath` objects, starting subsequent paths with `./`
`sel.xpath('/html').xpath('./body').xpath('./div[2]')

### `extract()` data from SelectorList
`sel.xpath('//p').extract_first()`
returns:
`<p>Hello World!</p>'`
alternatively, use:
`sel.xpath('//p')[0]
- remember that `Selector`s only contain one piece of data

### Example with `requests` package
```
from scrapy import Selector
import requests

# Create the string html containing the HTML source
html = requests.get( url ).content

# Create the Selector object sel from html
sel = Selector(text=html )

# Print out the number of elements in the HTML document
print( "You have found: ", len(sel.xpath('//*')), " elements" )
```

# CSS
## Locators
- **Cascading Style Sheets**: describe how elements are displayed on a screen

Syntax differences:
| Xpath | CSS                        |
| ----- | -------------------------- |
| `/`   | ` > ` (ignores first char) |
| `//`  | ` ` (ignores first char)   |
| `[N]` | `:nth-of-type(N)`          |
e.g.:
- `xpath = '/html/body//div/p[2]'`
- `css = 'html > body div > p:nth-of-type(2)'
## Attributes
- to find an element by class, use `.`
  - e.g. `p.class-1 > a`
- to find an element by id, use a pound sign `#`
  - e.g. `div#uid`
`css_locator = 'div#uid > p.class1'` == `xpath_locator = '//div[@id="uid"]/span//h4'
- use the `*` operator: 
`#uid > *` --> all children of the element that has `id=uid`

## Attribute & text selection
`xpath = <xpath-to-element>/@attr-name`
`css_locator = <css-to-element>::attr(attr-name)`
`'//div[@id="uid"]/a/@href'` == `div#uid > a::attr(href)'`

### Text extraction
- `sel.xpath('//p[@id="p-example"]/text()').extract()` (use `//text()` for all future generations' text 
- `sel.css('p#p-example::text').extract()` (use ` ` before `::text` for all future generations' text
- in both notations, extracted text is broken up by element
  - may need to `" ".join(selected_text)`

# Response objects
- response has all the tools as selectors
- it also keeps track of the URL where the HTML code was loaded from
- response helps us move from one site to another via the `follow` method
`response.follow(new_url)`
```
# Get the URL to the website loaded in response
this_url = response.url

# Get the title of the website loaded in response
this_title = response.xpath( '/html/head/title/text()' ).extract_first()

# Print out our findings
print_url_title( this_url, this_title )
```

```
# Create a CSS Locator string to the desired hyperlink elements
css_locator = 'a.course-block__link'

# Select the hyperlink elements from response and sel
response_as = response.css( css_locator )
sel_as = sel.css( css_locator )

# Examine similarity
nr = len( response_as )
ns = len( sel_as )
for i in range( min(nr, ns, 2) ):
  print( "Element %d from response: %s" % (i+1, response_as[i]) )
  print( "Element %d from sel: %s" % (i+1, sel_as[i]) )
  print( "" )
```

# Building a spider
- crawls the web through multiple pages, follows links, and scrapes those pages automatically according to the procedures we've programmed

```
import scrapy
from scrapy.crawler import CrawlerProcess

classDCspider( scrapy.Spider ): 
  name = 'dc_spider'
  
  def start_requests( self ):
    urls = [ 'https://www.datacamp.com/courses/all' ]
      for url in urls:
      yield scrapy.Request( url = url, callback = self.parse )
      def parse( self, response ):
      # simple example: write out the html 
      html_file = 'DC_courses.html'
      with open( html_file, 'wb' ) as fout: 
        fout.write( response.body )
```

## Start requests
```
def start_requests(self):
  list_of_urls_to_scrape = list()
  for url in urls:
    yield scrapy.Request(url= url, callback = self.parse)
  
```

## Parse & Crawl!
- we can call the `parse` method whatever we want 
```
def parse(self, response):
  links = response.css('div.course-block > a::attr(href)').extract()
  for link in links:
    yield response.follow(url = link, callback = self.parse2)

def parse2(self, response):
  # parse secondary sites!