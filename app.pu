from flask import Flask, request, jsonify
import requests
import base64
import os

app = Flask(__name__)

API_URL = 'https://lan94.ru/json-rpc'
USERNAME = os.getenv("USERNAME")
PASSWORD = os.getenv("PASSWORD")

auth_header = {
    'Authorization': 'Basic ' + base64.b64encode(f'{USERNAME}:{PASSWORD}'.encode()).decode()
}

@app.route('/get_credentials', methods=['POST'])
def get_credentials():
    data = request.get_json()
    phone = data.get('phone')

    payload = {
        "jsonrpc": "2.0",
        "method": "client.list",
        "params": {
            "filters": [
                {"name": "mobile", "op": "eq", "val": phone}
            ]
        },
        "id": 1
    }

    response = requests.post(API_URL, json=payload, headers=auth_header)
    if response.status_code != 200:
        return jsonify({"status": "error", "message": "Ошибка связи с биллингом"}), 500

    clients = response.json().get("result", {}).get("data", [])

    if not clients:
        return jsonify({"status": "error", "message": "Абонент не найден"}), 404

    login = clients[0].get("login")
    password = clients[0].get("password") or "Пароль скрыт"

    return jsonify({"status": "ok", "login": login, "password": password})
