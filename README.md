import flask
from flask import request, jsonify
import random

app = flask.Flask(__name__)
app.config["DEBUG"] = True

# Simulierte Marktpreise (Live-Daten später über API einbinden)
market_prices = {
    "Tibber": random.uniform(0.05, 0.30),  # €/kWh
    "Octopus Energy": random.uniform(0.04, 0.28),
    "EPEX Spot": random.uniform(0.03, 0.25),
}

# KI-Algorithmus zur Preisprognose (vereinfachtes Modell)
def predict_best_price():
    lowest_price = min(market_prices.values())
    best_provider = min(market_prices, key=market_prices.get)
    return {"best_price": lowest_price, "provider": best_provider}

# Kaufen von Strom zum günstigsten Preis
@app.route('/api/buy_energy', methods=['POST'])
def buy_energy():
    data = request.json
    amount = data.get("amount")  # kWh
    best_price_info = predict_best_price()
    
    cost = amount * best_price_info["best_price"]
    return jsonify({
        "message": "Energie erfolgreich gekauft!",
        "amount_kWh": amount,
        "price_per_kWh": best_price_info["best_price"],
        "total_cost": cost,
        "provider": best_price_info["provider"],
    })

# Verkauf von Strom zum höchsten Marktpreis
@app.route('/api/sell_energy', methods=['POST'])
def sell_energy():
    data = request.json
    amount = data.get("amount")  # kWh
    highest_price = max(market_prices.values())
    best_selling_provider = max(market_prices, key=market_prices.get)

    revenue = amount * highest_price
    return jsonify({
        "message": "Energie erfolgreich verkauft!",
        "amount_kWh": amount,
        "price_per_kWh": highest_price,
        "total_revenue": revenue,
        "buyer_provider": best_selling_provider,
    })

# Abrufen der aktuellen Marktpreise
@app.route('/api/market_prices', methods=['GET'])
def get_market_prices():
    return jsonify(market_prices)

# KI-gestützte Empfehlung für Kauf/Verkauf
@app.route('/api/trade_recommendation', methods=['GET'])
def trade_recommendation():
    best_buy = predict_best_price()
    best_sell_price = max(market_prices.values())
    best_sell_provider = max(market_prices, key=market_prices.get)
    
    return jsonify({
        "recommendation": "BUY" if best_buy["best_price"] < best_sell_price else "HOLD",
        "best_buy_price": best_buy["best_price"],
        "buy_from": best_buy["provider"],
        "best_sell_price": best_sell_price,
        "sell_to": best_sell_provider,
        "potential_profit_per_kWh": best_sell_price - best_buy["best_price"],
    })

if __name__ == "__main__":
    app.run(debug=True)
