---
title: resnetimg
date: 2019-03-12 14:53:40
tags: [classification]
category:
- computer vision
- classification

---

# Resnet

## deep learning

{% link 百度 https://www.baidu.com %}

{% codeblock %}

def fetch_news_item(newsq):
    while True:
        news = newsq.get()
        if not news:
            break
        try:
            req = urllib.request.Request(news['link'])
            req.add_header('User-agent', 'Mozilla 5.10')
            response = request.urlopen(req)
            time.sleep(1)
            html = response.read()
            soup = BeautifulSoup(html, "lxml")
            # print(soup)
            source = soup.find(id='media_name')
            if source:
                news['source'] = source.get_text()
            else:
                news['source'] = "sina"

            content = soup.find(id='artibody')
            if content:
                news['content'] = content.get_text()

        except Exception as e:
            print("Crawl exception", e)

        try:
            day = news['link'].split("/")[-2]
            path = news['link'].split(".")[-2].split("/")[-1]
            # print(day, path)
            dirp = os.path.join(os.path.curdir, "data/sina/%s" % day)
            if not os.path.isdir(dirp):
                os.mkdir(dirp)
            with open(os.path.join(dirp, "%s.json" % path), "w",
                      encoding="utf8") as f:
                json.dump(news, f, ensure_ascii=False)
        except Exception as e:
            print("write exception", e)


{% endcodeblock %}


# 参考文献

{% blockquote %}

He, K., Zhang, X., Ren, S., & Sun, J. (2016). Deep residual learning for image recognition. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 770-778).

{% endblockquote %}

# 架构
<img src='resnet.png' width='80%'>
<!-- ![](resnet.png) -->
<!-- {% asset_img resnet.png This is resnet architecture %} -->
