import os
import time
import json
import threading
import random
from flask import Flask, render_template_string, jsonify, request
from cryptography.fernet import Fernet
from colorama import Fore, Style, init

init(autoreset=True)

# ==========================================
# 1. النواة والأمن (The Core & Crypto)
# ==========================================
class KCF_Matrix_Core:
    def __init__(self):
        self.key = Fernet.generate_key()
        self.cipher = Fernet(self.key)
        self.nodes = {}  # قاعدة بيانات الأجهزة المرتبطة
        self.logs = []
        self.dictionary = {
            "OSINT": "Open Source Intelligence",
            "AES-256": "Advanced Encryption Standard",
            "Node": "An active endpoint in the framework"
        }

    def log_event(self, msg):
        timestamp = time.strftime("%H:%M:%S")
        self.logs.append(f"[{timestamp}] {msg}")

# ==========================================
# 2. خادم الويب واللوحة التفاعلية (The Neon Dashboard)
# ==========================================
app = Flask(__name__)
matrix = KCF_Matrix_Core()

HTML_TEMPLATE = """
<!DOCTYPE html>
<html>
<head>
    <title>KCF ETERNAL MATRIX</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body { background-color: #050505; color: #00ff41; font-family: 'Courier New'; padding: 20px; }
        .neon-border { border: 2px solid #00ff41; box-shadow: 0 0 15px #00ff41; padding: 20px; margin-bottom: 20px; border-radius: 10px; }
        h1 { color: #ff00ff; text-shadow: 0 0 10px #ff00ff; }
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
        canvas { max-width: 100%; background: #111; border-radius: 5px; }
    </style>
</head>
<body>
    <h1>KCF - ETERNAL COMMAND CENTER 👑</h1>
    <div class="grid">
        <div class="neon-border">
            <h3>Connected Nodes Status</h3>
            <canvas id="trafficChart"></canvas>
        </div>
        <div class="neon-border">
            <h3>System Logs</h3>
            <div id="logBox" style="height: 200px; overflow-y: scroll; font-size: 12px; text-align: left;">
                {% for log in logs %} <p>> {{ log }}</p> {% endfor %}
            </div>
        </div>
    </div>

    <script>
        const ctx = document.getElementById('trafficChart').getContext('2d');
        new Chart(ctx, {
            type: 'line',
            data: {
                labels: ['1s', '2s', '3s', '4s', '5s', '6s'],
                datasets: [{
                    label: 'Signal Strength (dBm)',
                    data: [12, 19, 3, 5, 2, 3],
                    borderColor: '#ff00ff',
                    tension: 0.4
                }]
            }
        });
        setInterval(() => { location.reload(); }, 5000); // تحديث تلقائي لمحاكاة اللوحة التفاعلية
    </script>
</body>
</html>
"""

@app.route('/')
def index():
    return render_template_string(HTML_TEMPLATE, logs=matrix.logs)

@app.route('/api/register', methods=['POST'])
def register_node():
    node_id = request.json.get('id')
    matrix.nodes[node_id] = "ONLINE"
    matrix.log_event(f"New Node Detected: {node_id}")
    return jsonify({"status": "Success", "key": "SECURED"})

# ==========================================
# 3. محرك العميل (The Node Agent)
# ==========================================
def node_agent_mock(node_id):
    """محاكاة لعميل يتم تثبيته على الأجهزة الأخرى"""
    time.sleep(5)
    matrix.log_event(f"Agent {node_id} established handshake...")
    while True:
        # محاكاة إرسال بيانات استخباراتية للخادم
        time.sleep(10)
        matrix.log_event(f"Data packet received from {node_id}")

# ==========================================
# 4. المحرك الرئيسي (Master Launcher)
# ==========================================
def run_matrix():
    print(Fore.RED + Style.BRIGHT + r"""
  ███████╗████████╗███████╗██████╗ ███╗   ██╗ █████╗ ██╗
  ██╔════╝╚══██╔══╝██╔════╝██╔══██╗████╗  ██║██╔══██╗██║
  █████╗     ██║   █████╗  ██████╔╝██╔██╗ ██║███████║██║
  ██╔══╝     ██║   ██╔══╝  ██╔══██╗██║╚██╗██║██╔══██║██║
  ███████╗   ██║   ███████╗██║  ██║██║ ╚████║██║  ██║███████╗
  ╚══════╝   ╚═╝   ╚══════╝╚═╝  ╚═╝╚═╝  ╚═══╝╚═╝  ╚═╝╚══════╝
    [ KCF FINAL SYNTHESIS ] [ OWNER: صاحب الجواهر ]
    """)
    
    matrix.log_event("Initializing Eternal Core...")
    matrix.log_event("Starting Web Dashboard on port 5000...")

    # تشغيل الخادم والعملاء في خيوط (Threads) منفصلة
    threading.Thread(target=lambda: app.run(port=5000, debug=False, use_reloader=False)).start()
    threading.Thread(target=node_agent_mock, args=("NODE-ALPHA",)).start()
    threading.Thread(target=node_agent_mock, args=("NODE-BETA",)).start()

    while True:
        cmd = input(f"{Fore.GREEN}KCF_MASTER > {Fore.WHITE}").strip().lower()
        if cmd == "help":
            print("الأوامر: status, dict, logs, exit")
        elif cmd == "status":
            print(f"Nodes: {matrix.nodes}")
        elif cmd == "dict":
            print(json.dumps(matrix.dictionary, indent=2))
        elif cmd == "exit":
            os._exit(0)

if __name__ == "__main__":
    run_matrix()
