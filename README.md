Upton
==========
Upton is a framework for easy web-scraping with a useful debug mode that doesn't hammer your target's servers. It does the repetitive parts of writing scrapers, so you only have to write the unique parts for each site.

Documentation
----------------------

Upton works best if your scraper class inherits from Upton::Scraper, e.g. 

     class MyScraper < Upton::Scraper 
     ...
     end

Upton::Scraper is implemented as an abstract class. For basic use cases, you only have to implement one method -- the `get_index` method -- and a block argument for `scrape` to do something with the scraped pages. 

Upton operates on the theory that, for most scraping projects, you need to
scrape two types of pages:

1. Index pages, which list instance pages. For example, a job search 
    site's search page or a newspaper's homepage.
2. Instance pages, which represent the goal of your scraping, e.g.
    job listings or news articles.


In more complicated cases, you may need to write additional methods; for example, if you need to log in to a site before scraping the instance pages, you would need to override the scrape method in your subclass.

You get, for free, methods like `get_page(url, stash=false)` which, well, gets a page. That's not very special. The more interesting part is that `get_page(url, stash=false)` transparently stashes the response of each request. Whenever you repeat a request with `true` as the second parameter, the stashed HTML is returned without going to the server. This is helpful in the development stages of a project when you're testing some aspect of the code and don't want to hit a server each time.

Upton also sleeps (by default) 30 seconds between non-stashed requests, to reduce load on the server you're scraping. This is configurable with the `@nice_sleep_time` option.

Example
----------------------
If you want to scrape ProPublica's website with Upton, this is how you'd do it. (Scraping our [RSS feed](http://feeds.propublica.org/propublica/main) would be smarter, but not every site has a full-text RSS feed...)


      class ProPublicaScraper < Upton::Scraper
        def get_index
          url = "http://www.propublica.org"
          doc = Nokogiri::HTML(self._get_page(url, true))
          doc.css("section#river section h1 a").to_a.map{|l| + l["href"] } #map the Nokogiri link elements to a string representation of a URL.
        end
      end

      ProPublicaScraper.new.scrape do |article_string|
        puts "here is the full text of the ProPublica article: \n #{article_string}"
        #or, do other stuff here.
      end


Why "Upton"
----------------------
Upton Sinclair was a pioneering, muckraking journalist who is most famous for _The Jungle_, a novel portraying the reality of immigrant labor struggles in Chicago meatpacking plants at the start of the 1900s. Upton, the gem, sprang out of a ProPublica project pertaining to labor issues.

License (MIT)
------------------------

Copyright (c) 2013 ProPublica

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

