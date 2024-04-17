from flask import Flask, render_template
import requests
from bs4 import BeautifulSoup

app = Flask(__name__)                                                                                        

def get_news():
    url = 'https://asia24.media/main/?utm_source=yandex&utm_medium=cpc&utm_campaign=81844203&utm_content=13279466221&utm_term=%D0%BD%D0%BE%D0%B2%D0%BE%D1%81%D1%82%D0%B8%20%D0%BA%D0%B0%D0%B7%D0%B0%D1%85%D1%81%D1%82%D0%B0%D0%BD%D0%B0&yclid=3872665976597643263'
    response = requests.get(url)

    if response.status_code == 200:
        soup = BeautifulSoup(response.text, 'html.parser')
        news_list = []
        articles = soup.find_all('div', class_='b-section-item')
        for article in articles:
            title = article.find('h3', class_="b-section-item__title").text.strip()
            # summary = article.find('div', class_='b-section-item__desc').text.strip()
            link = article.find('a')['href']

            news_list.append({'title': title,  'link': link})
        print("articles")
        print(articles)

        return news_list


@app.route('/')
def index():
    news = get_news()
    return render_template('index.html', news = news)

if __name__ == '__main__':
    app.run(port=5000, debug=True)





<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Новостной агрегатор</title>
</head>
<body>
    <h1>Новостной агрегатор</h1>
    <ul>                                                                                                                       
        {% for article in news %}
            <li>
                <h2><a href="#">{{ article.title }}</a></h2>
                <p>{{ article }}</p>
            </li>
        {% endfor %}
    </ul>
</body>
</html>
