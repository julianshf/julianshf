import requests

# API-Schlüssel (Platzhalter, ersetze durch echte Schlüssel)
API_KEYS = {
    "Tibber": "DEIN_TIBBER_API_KEY",
    "Sonnen": "DEIN_SONNEN_API_KEY",
    "Octopus Energy": "DEIN_OCTOPUS_API_KEY",
    "Stripe": "DEIN_STRIPE_API_KEY",
    "PayPal": "DEIN_PAYPAL_API_KEY",
    "BitPay": "DEIN_BITPAY_API_KEY",
    "Ethereum": "DEIN_ETHERSCAN_API_KEY"
}

# APIs für Smart Meter & Energieverbrauch
SMART_METER_APIS = {
    "Tibber": "https://api.tibber.com/v1-beta/gql",
    "Sonnen": "https://my.sonnen.de/api/v2",
    "Octopus Energy": "https://api.octopus.energy/v1/"
}

# APIs für Zahlungsanbieter
PAYMENT_APIS = {
    "Stripe": "https://api.stripe.com/v1",
    "PayPal": "https://api.paypal.com/v2",
    "BitPay": "https://bitpay.com/api/",
    "Ethereum": "https://etherscan.io/api"
}

# Echtzeit-Stromverbrauch abrufen
def get_smart_meter_data(provider, user_token):
    if provider in SMART_METER_APIS:
        url = SMART_METER_APIS[provider]
        headers = {"Authorization": f"Bearer {API_KEYS.get(provider, '')}"}
        response = requests.get(url, headers=headers)
        return response.json() if response.status_code == 200 else {"error": "API-Zugriff fehlgeschlagen"}
    return {"error": "Anbieter nicht gefunden"}

# Zahlung verarbeiten
def process_payment(provider, amount, currency="EUR", user_id="user1"):
    if provider in PAYMENT_APIS:
        url = PAYMENT_APIS[provider]
        payment_data = {
            "amount": amount * 100,  # Stripe verarbeitet Beträge in Cent
            "currency": currency,
            "description": f"Stromhandel für {user_id}",
        }
        headers = {"Authorization": f"Bearer {API_KEYS.get(provider, '')}"}
        response = requests.post(url, json=payment_data, headers=headers)
        return response.json() if response.status_code == 200 else {"error": "Zahlung fehlgeschlagen"}
    return {"error": "Zahlungsanbieter nicht gefunden"}
