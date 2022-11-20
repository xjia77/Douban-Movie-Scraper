## ScrapyDouban


Python3-based Douban Movie/Douban Book Scarpy crawler for cover downloading + data crawling + review entry.

The purpose of maintaining this project is to share some of my practice in the process of using Scrapy, the project covers about 80% of my knowledge of Scrapy, I hope to help friends who are learning Scrapy, please note that the current version of the project is Scrapy 2.5.0.


### Docker
-------
Project contains douban scrapyd douban db douban adminer three containers.

The douban_scrapyd container is based on [python:3.9-slim-buster](https://pythonspeed.com/articles/base-image-python-docker-images/), the default installed Python3 libraries are scrapy scrapyd pymysql pillow arrow, default mapping port 6800:6800 to facilitate user access to scrapyd management interface via host IP:6800, login required parameters, username:scrapyd password:public.

The douban_db container is based on mysql:8, root password is public, and the default initialization is to import the docker/mysql/douban.sql file to the douban database.

douban_adminer container is based on adminer:4, default mapping port 8080:8080 to facilitate users to access the database management interface through the host IP:8080, login required parameters, server:mysql username:root password:public.



### Project SQL
------

The path to the SQL file used by the project is docker/mysql/douban.sql.

### Collection Process
-------

    First collect Subject ID --> then crawl the detail page by subject ID to collect data --> finally collect comments by Subject ID

### method
-------
    $ git clone https://github.com/xjia77/ScrapyDouban.git
    # Build and run containers
    $ cd ./ScrapyDouban/docker
    $ sudo docker-compose up --build -d
    # enter douban_scrapyd container
    $ sudo docker exec -it douban_scrapyd bash
    # enter scrapy content
    $ cd /srv/ScrapyDouban/scrapy
    $ scrapy list
    # Grabbing movie data
    $ scrapy crawl movie_subject # collect movie Subject ID
    $ scrapy crawl movie_meta # collect movie data
    $ scrapy crawl movie_comment # collect movie comment
    # Grabbing book data
    $ scrapy crawl book_subject # collect book Subject ID
    $ scrapy crawl book_meta # collect book data
    $ scrapy crawl book_comment # collect book comment

If you want to make changes to your code more easily while testing, you can mount your project in the scrapy directory to the douban_scrapyd container.
If you are used to working with scrapyd, you can deploy project directly to the douban_scrapyd container via scrapyd-client.

### Proxy IP
--------

Due to douban's anti-crawler mechanism, the only way to bypass it now is through a proxy IP. ProxyMiddleware middleware is not enabled in the default settings.py. If you really need to use Douban's data to do some research, you can go rent a paid proxy pool.


### image download
--------

douban.pipelines.CoverPipeline processes the cover download logic by filtering spider.name, and the save path of the downloaded image files is the /srv/ScrapyDouban/storage directory of the douban_scrapy container.
