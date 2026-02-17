```markdown
# 📚 MenuBuilder Documentation Scraper (Google Colab)

Ushbu loyiha [docs.menubuilder.cc](https://docs.menubuilder.cc/) saytidagi barcha hujjatlarni avtomatik ravishda yig'ib (scrape qilib), bitta **Markdown (.md)** faylga aylantirib beradi.

## 🎯 Muammo
Bizga `MenuBuilder` bo'yicha to'liq dokumentatsiya kerak edi, lekin saytda "Barchasini yuklab olish" (Download All) tugmasi yo'q. Yuzlab sahifalarni qo'lda ko'chirib chiqish juda ko'p vaqt oladi.

## 💡 Yechim
Biz **Python** va **Google Colab** yordamida skript yozdik. Bu skript:
1. Saytning barcha bo'limlariga kirib chiqadi.
2. HTML kodlarni tozalaydi.
3. Ularni chiroyli **Markdown** formatiga o'tkazadi.
4. Barcha ma'lumotni bitta fayl qilib taqdim etadi.

## 🚀 Qanday ishlatiladi? (Qo'llanma)

Sizga Python o'rnatish shart emas. Hammasi brauzerda ishlaydi.

1. **[Google Colab-ni ochish](https://colab.research.google.com/)** tugmasini bosing va "New Notebook" tanlang.
2. Quyidagi kodni nusxalab, Colab katagiga tashlang.
3. Chap tomondagi **Play (▶️)** tugmasini bosing.
4. 1-2 daqiqa kuting.
5. Jarayon tugagach, `MenuBuilder_Docs_Full.md` fayli avtomatik yuklanadi (yoki chapdagi papka belgisidan topishingiz mumkin).

## 💻 Kod (Script)

```python
# Kerakli kutubxonalarni o'rnatamiz
!pip install requests beautifulsoup4 markdownify -q

import requests
from bs4 import BeautifulSoup
from markdownify import markdownify as md
from urllib.parse import urljoin
import time
from google.colab import files

# --- SOZLAMALAR ---
BASE_URL = "https://docs.menubuilder.cc/"
OUTPUT_FILENAME = "MenuBuilder_Docs_Full.md"
visited_urls = set()
all_content = []

def get_soup(url):
    try:
        response = requests.get(url, headers={'User-Agent': 'Mozilla/5.0 (Colab Bot)'})
        if response.status_code == 200:
            return BeautifulSoup(response.content, 'html.parser')
    except:
        return None

def process_page(url):
    if url in visited_urls: return
    visited_urls.add(url)
    
    # Keraksiz sahifalarni o'tkazib yuboramiz
    if any(x in url for x in ['/search', '/login', '#']): return

    print(f"⏳ Yuklanmoqda: {url}")
    soup = get_soup(url)
    if not soup: return

    # Asosiy kontentni olamiz
    content_div = soup.find('div', class_='page-content')
    if content_div:
        title = soup.find('h1').get_text(strip=True) if soup.find('h1') else "No Title"
        text_content = md(str(content_div), heading_style="ATX")
        
        all_content.append(f"\n\n# {title}\nURL: {url}\n\n{text_content}")

    # Ichki havolalarni qidiramiz
    for link in soup.find_all('a', href=True):
        full_url = urljoin(BASE_URL, link['href'])
        if BASE_URL in full_url and '/books/' in full_url:
            process_page(full_url)
            time.sleep(0.1) # Serverni yuklamaslik uchun pauza

print("🚀 Boshladik...")
process_page(BASE_URL)

# Faylni saqlash
with open(OUTPUT_FILENAME, "w", encoding="utf-8") as f:
    f.write(f"# MenuBuilder Documentation\nGenerated: {time.strftime('%Y-%m-%d')}\n")
    f.write("".join(all_content))

print("✅ Tayyor! Fayl yuklanmoqda...")
try: files.download(OUTPUT_FILENAME)
except: print("⚠️ Faylni chap menyudan yuklab oling.")
```

## 🤖 Natija bilan nima qilamiz?
Hosil bo'lgan `.md` faylni **ChatGPT**, **Claude** yoki **Gemini** ga yuklang va shunday deb yozing:

> *"Mana MenuBuilder bo'yicha to'liq qo'llanma. Endi men senga bot sozlash bo'yicha savollar beraman, shu faylga asoslanib javob ber."*

---
*Muallif: [Abrahamjon]*
```
