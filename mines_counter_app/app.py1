import time
import threading
from flask import Flask, render_template, jsonify, request

app = Flask(__name__)

MAX_VALUES = [
    60,    # Шахта 1
    150,   # Шахта 2
    240,   # Шахта 3
    330,   # Шахта 4
    420,   # Шахта 5
    510,   # Шахта 6
    600,   # Шахта 7
    590,   # Шахта 8
    780,   # Шахта 9
    870,   # Шахта 10
    1000,  # Шахта 11
    3600,  # Мистик
    3600,  # Маяк
    600    # Начальная шахта
]

mines_data = [{"current": max_val, "maximum": max_val} for max_val in MAX_VALUES]
stop_events = [threading.Event() for _ in MAX_VALUES]

def countdown(mine_id, stop_event):
    while not stop_event.is_set():
        time.sleep(1)
        if mines_data[mine_id]["current"] > 0:
            mines_data[mine_id]["current"] -= 1
        else:
            mines_data[mine_id]["current"] = mines_data[mine_id]["maximum"]

for i, stop_event in enumerate(stop_events):
    threading.Thread(target=countdown, args=(i, stop_event), daemon=True).start()

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/get_mine_data')
def get_mine_data():
    return jsonify(mines_data)

@app.route('/update_mine', methods=['POST'])
def update_mine():
    data = request.get_json()
    mine_id = data['mine_id']
    new_value = int(data['current'])
    if 0 <= new_value <= MAX_VALUES[mine_id]:
        mines_data[mine_id]["current"] = new_value
        return jsonify({"status": "success", "new_value": new_value})
    else:
        return jsonify({"status": "error", "message": "Invalid value"}), 400

@app.route('/update_max_value', methods=['POST'])
def update_max_value():
    data = request.get_json()
    mine_id = data['mine_id']
    new_max = int(data['maximum'])
    if new_max > 0:
        MAX_VALUES[mine_id] = new_max
        mines_data[mine_id]["maximum"] = new_max
        return jsonify({"status": "success", "new_max": new_max})
    else:
        return jsonify({"status": "error", "message": "Invalid maximum value"}), 400

if __name__ == '__main__':
    app.run(host="0.0.0.0", port=5000)
