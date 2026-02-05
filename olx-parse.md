## Как я парсил OLX.id
> Вопреки утверждениям что OLX очень сильно защищен от парсинга и нужно быть молодцом чтобы получить что то оттуда - метод стандартный.

* Взял URL `https://www.olx.co.id/bali_g2000002/q-villa` : это все виллы по Бали, по 20 штук на странице -много.
* Сгенерил в Gemini curl запрос.
* Начал пробовать парсить. Вывод получил сразу.
### 1) Сохранил выход curl:
~~~sh
hakim@1hakim:bali$ curl -X GET "https://www.olx.co.id/bali_g2000002/q-villa"      -H "User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.0.0 Safari/537.36"      -H "Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8"      -H "Accept-Language: id-ID,id;q=0.9,en-US;q=0.8,en;q=0.7"      -H "Referer: https://www.google.com"      -H "Sec-Ch-Ua: \"Not A(Brand\";v=\"99\", \"Google Chrome\";v=\"121\", \"Chromium\";v=\"121\""      -H "Sec-Fetch-Dest: document"      -H "Sec-Fetch-Mode: navigate"      -H "Sec-Fetch-Site: cross-site"      --compressed > raw.txt
~~~

### 2) Запарсил raw.txt таким скриптом:
~~~bash
#!/bin/bash

INPUT="raw.txt"
OUTPUT="itemsList.md"

echo "# 🏝️ Bali Properties from OLX" > $OUTPUT
echo "" >> $OUTPUT

# Используем awk для обработки многострочных карточек
awk '
BEGIN {
    count = 0
    print "# 🏝️ Bali Properties from OLX"
    print ""
    print "> *Extracted on: '"$(date +'%Y-%m-%d')"'*"
    print ""
}

# Ищем начало карточки
/<li id="item-card-/ {
    in_card = 1
    card_data = ""
}

# Собираем данные карточки
in_card {
    card_data = card_data $0 "\n"
}

# Конец карточки
in_card && /<\/li>/ {
    in_card = 0
    count++

    # Извлекаем данные
    link = ""
    title = ""
    price = ""
    location = ""
    img = ""

    # Ссылка
    if (match(card_data, /href="([^"]*)"/, arr)) {
        link = "https://www.olx.co.id" arr[1]
    }

    # Заголовок
    if (match(card_data, /data-aut-id="itemTitle">([^<]*)/, arr)) {
        title = arr[1]
    }

    # Цена
    if (match(card_data, /data-aut-id="itemPrice">([^<]*)/, arr)) {
        price = arr[1]
    }

    # Локация
    if (match(card_data, /data-aut-id="item-location">([^<]*)/, arr)) {
        location = arr[1]
    }

    # Изображение
    if (match(card_data, /https:\/\/apollo\.olx\.co\.id:443\/v1\/files\/[^;]*/, arr)) {
        img = arr[0]
    }

    # Выводим карточку
    print "## 🏠 " count ". " title
    print ""
    if (img != "") {
        print "![Property](" img ")"
        print ""
    }
    if (price != "") {
        print "**💰 Price:** " price
        print ""
    }
    if (location != "") {
        print "**📍 Location:** " location
        print ""
    }
    if (link != "") {
        print "**🔗 [View on OLX](" link ")**"
        print ""
    }
    print "---"
    print ""
}

END {
    if (count == 0) {
        print "## ⚠️ No structured cards found"
        print ""
        print "The HTML might be minified or structured differently."
        print ""
        print "### Raw property mentions found:"
        print ""

        # Простой поиск заголовков
        while (getline < "'"$INPUT"'") {
            if (match($0, /Tanah|Villa|Property|Lokasi|Dekat/, arr)) {
                if (length($0) < 200 && length($0) > 20) {
                    print "- " $0
                }
            }
        }
    } else {
        print "## 📊 Summary"
        print "Found **" count " properties** in total"
    }
    print ""
    print "*Generated from OLX source data*"
}
' "$INPUT" > "$OUTPUT"

echo "✅ Created $OUTPUT"
~~~

---

## Кому интересно - на момент 05.02.2026 olx.id давал такой HTML:
~~~html
<!--- до этого много html: привожу только полезную часть --->
<div><ul class="_266Ly _10aCo" data-aut-id="itemsList"><li id="item-card-941574041" data-aut-id="itemBox" data-aut-category-id="4827" class="_1DNjI"><a class="_2cbZ2" href="/item/tanah-langka-lokasi-strategis-dekat-pantai-jimbaran-dan-bandara-i-gusti-ngurah-rai-iid-941574041"><figure class="_3UrC5" data-aut-id="itemImage"><div class="_12wQG"><div class="_2Lq5Y"><picture><img src="https://statics.olx.co.id/olxid/seller/highlights.svg" class="TDfjt" loading="lazy" alt="Featured"/></picture></div></div><img src="https://apollo.olx.co.id:443/v1/files/69573c44e4d0b-ID/image;s=272x0" alt="Tanah Langka Lokasi Strategis Dekat Pantai Jimbaran dan Bandara I Gusti Ngurah Rai" sizes="(min-width: 1280px) 15vw, (min-width: 1024px) 20vw, (min-width: 960px) 25vw, (min-width: 540px) 25vw, (min-width: 320px) 30vw, 35vw" srcSet="https://apollo.olx.co.id:443/v1/files/69573c44e4d0b-ID/image;s=100x200;q=60 100w,
                    https://apollo.olx.co.id:443/v1/files/69573c44e4d0b-ID/image;s=200x400;q=60 200w,
                    https://apollo.olx.co.id:443/v1/files/69573c44e4d0b-ID/image;s=300x600;q=60 300w,
                    https://apollo.olx.co.id:443/v1/files/69573c44e4d0b-ID/image;s=400x800;q=60 400w,
                    https://apollo.olx.co.id:443/v1/files/69573c44e4d0b-ID/image;s=600x1200;q=60 600w" class="_2hBzJ" fetchpriority="high"/><noscript><img src="https://apollo.olx.co.id:443/v1/files/69573c44e4d0b-ID/image;s=272x0" alt="Tanah Langka Lokasi Strategis Dekat Pantai Jimbaran dan Bandara I Gusti Ngurah Rai" sizes="(min-width: 1280px) 15vw, (min-width: 1024px) 20vw, (min-width: 960px) 25vw, (min-width: 540px) 25vw, (min-width: 320px) 30vw, 35vw" srcSet="https://apollo.olx.co.id:443/v1/files/69573c44e4d0b-ID/image;s=100x200;q=60 100w,
                    https://apollo.olx.co.id:443/v1/files/69573c44e4d0b-ID/image;s=200x400;q=60 200w,
                    https://apollo.olx.co.id:443/v1/files/69573c44e4d0b-ID/image;s=300x600;q=60 300w,
                    https://apollo.olx.co.id:443/v1/files/69573c44e4d0b-ID/image;s=400x800;q=60 400w,
                    https://apollo.olx.co.id:443/v1/files/69573c44e4d0b-ID/image;s=600x1200;q=60 600w" class="_2hBzJ" fetchpriority="high"/></noscript></figure><div class="fTZT3"><div class="_11Z7Y"></div><div class="MWCw6"><span class="_2Ks63 _16PdY" data-aut-id="itemPrice">Rp 700.000.000</span></div><span class="_2poNJ" data-aut-id="itemTitle">Tanah Langka Lokasi Strategis Dekat Pantai Jimbaran dan Bandara I Gusti Ngurah Rai</span><div class="_3rmDx"><span class="_2VQu4" data-aut-id="item-location">Kuta Selatan, Kab. Badung</span><span class="_2jcGx"><span>13 Jan</span></span></div></div></a><span class="_1Tqbb _18Wg3"><button type="button" class="rui-3a8k1 _29mJd favoriteOff" role="button" tabindex="0" data-aut-id="btnFav" title="Favourite"><svg width="24px" height="24px" viewBox="0 0 1024 1024" data-aut-id="icon" class="" fill-rule="evenodd"><path class="rui-w4DG7" d="M830.798 448.659l-318.798 389.915-317.828-388.693c-20.461-27.171-31.263-59.345-31.263-93.033 0-85.566 69.605-155.152 155.152-155.152 72.126 0 132.752 49.552 150.051 116.364h87.777c17.299-66.812 77.905-116.364 150.051-116.364 85.547 0 155.152 69.585 155.152 155.152 0 33.687-10.802 65.862-30.293 91.811zM705.939 124.121c-80.853 0-152.204 41.425-193.939 104.204-41.736-62.778-113.086-104.204-193.939-104.204-128.33 0-232.727 104.378-232.727 232.727 0 50.657 16.194 98.948 47.806 140.897l328.766 402.133h100.189l329.716-403.355c30.662-40.727 46.856-89.018 46.856-139.675 0-128.349-104.398-232.727-232.727-232.727z"></path></svg></button></span></li>
<!--- и еще 19 таких же карточек --->
</ul>
</div>
~~~
