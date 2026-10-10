import json
import os
import urllib.parse
import urllib.request

LEVEL = 4202.0
SYMBOL = "XAU/USD"
STATE_FILE = "state.json"

TG_TOKEN = os.environ["TELEGRAM_TOKEN"]
TG_CHAT = os.environ["TELEGRAM_CHAT_ID"]
API_KEY = os.environ["TWELVE_API_KEY"]


def get_json(url):
    with urllib.request.urlopen(url, timeout=20) as r:
        return json.loads(r.read().decode())


def get_price():
    q = urllib.parse.urlencode({"symbol": SYMBOL, "apikey": API_KEY})
    data = get_json("https://api.twelvedata.com/price?" + q)
    if "price" not in data:
        raise RuntimeError("Price error: " + str(data))
    return float(data["price"])


def send(text):
    body = urllib.parse.urlencode({"chat_id": TG_CHAT, "text": text}).encode()
    url = "https://api.telegram.org/bot%s/sendMessage" % TG_TOKEN
    urllib.request.urlopen(url, data=body, timeout=20)


def main():
    price = get_price()
    side = "above" if price >= LEVEL else "below"

    old = None
    if os.path.exists(STATE_FILE):
        with open(STATE_FILE) as f:
            old = json.load(f).get("side")

    if old is None:
        where = "بالاتر" if side == "above" else "پایین‌تر"
        send(
            "✅ ربات روشن شد.\n"
            "قیمت فعلی %s: %.2f\n"
            "هدف: %g (الان %s از هدف است)" % (SYMBOL, price, LEVEL, where)
        )
    elif old != side:
        word = "بالای" if side == "above" else "زیر"
        send("🔔 %s %s %g رفت.\nقیمت فعلی: %.2f" % (SYMBOL, word, LEVEL, price))

    with open(STATE_FILE, "w") as f:
        json.dump({"side": side}, f)

    print("price", price, "side", side)


if __name__ == "__main__":
    main()
