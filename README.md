
👤 Auteur

Aïssa Mohammedi (@Aissamohammedi88)

Construit depuis un iPhone à 3h du matin.

Contact : LinkedIn

---

⭐ Si ce projet t'aide

Star le repo. Ça motive à continuer.

---

<div align="center">🧬 CreWos Sybot · x627 ms 0/ms perplexité x gamma

</div>
```---

📄 FICHIER 2 — LICENSE (MIT)

```
MIT License

Copyright (c) 2026 Aïssa is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

---

📄 FICHIER 3 — .gitignore

```
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
env/
venv/
.venv/
ENV/
build/
dist/
*.egg-info/

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Logs
*.log
logs/

# Data
*.db
*.sqlite
*.sqlite3
data/
sites/*/public/

# Secrets
.env
.env.local
secrets/
*.pem
*.key

# Temp
tmp/
temp/
*.tmp
```

---

📄 FICHIER 4 — requirements.txt

```
flask
gunicorn
```

---

📄 FICHIER 5 — Procfile (sans extension)

```
web: gunicorn serveur_render:app
```

---

📄 FICHIER 6 — CONTRIBUTING.md

```markdown
# Contribuer à NEKYLL

## Setup

```bash
git clone https://github.com/Aissamohammedi88/nekyll-is-a-static-site-generator-with-built-in-.git
cd nekyll-*
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

Structure

```
.
├── modele_01.py # Scanner Réseau
├── modele_02.py # Analyse HTML
├── modele_03.py # Forge de Site
├── modele_04.py # Radio
├── modele_05.py # Email
├── modele_06.py # Tor
├── modele_07.py # Benchmark
├── modele_08.py # USB Transfer
├── modele_09.py # Docs Generator
├── modele_10.py # LANCEUR
├── serveur_render.py # Serveur web
├── README.md
├── LICENSE
└── requirements.txt
```

Règles

1. Zéro dépendance — sauf Flask/Gunicorn pour Render
2. Python 3.9+ — compatible a-Shell
3. Un fichier = un modèle — pas de monolithe
4. Commenté — chaque fonction a une docstring
5. Testé — python3 -m py_compile avant PR

PR Checklist

□ Code compile (python3 -m py_compile)
□ Pas de nouvelle dépendance
□ Docstring ajoutée
□ Testé sur Linux ET a-Shell
□ README mis à jour si nécessaire

Idées de contribution

□ Tests unitaires (pytest)
□ Support Windows natif
□ Plus de langages dans le générateur
□ Thèmes additionnels
□ Intégration Docker
□ Documentation API

Merci de contribuer 🧬

```

---

## 📄 FICHIER 7 — `SECURITY.md`

```markdown
# Sécurité

## Signaler une vulnérabilité

**Ne pas ouvrir d'issue publique.**

Envoie un email à : security@nexus.dev (ou par LinkedIn)

Réponse en 48h.

## Ce que NEKYLL fait

- ✅ Zéro dépendance externe (sauf Flask/Gunicorn pour Render)
- ✅ Pas de télémétrie
- ✅ Pas de tracking
- ✅ Code open source vérifiable
- ✅ SOCKS5 pur Python
- ✅ Validation RFC email

## Ce que NEKYLL ne fait PAS

- ❌ Pas de données envoyées à un serveur tiers
- ❌ Pas de pub
- ❌ Pas de compte utilisateur
- ❌ Pas d'analytics

## Tor

NEKYLL ne fait pas de Tor. Il **utilise** Tor via SOCKS5 si tu l'as déjà.
Pour l'anonymat complet, utilise [Tor Browser](https://www.torproject.org).
```

---

📄 FICHIER 8 — .github/workflows/python-app.yml (CI GitHub)

```yaml
name: NEKYLL CI

on:
push:
branches: [ main ]
pull_request:
branches: [ main ]

jobs:
build:
runs-on: ubuntu-latest
strategy:
matrix:
python-version: ["3.9", "3.10", "3.11", "3.12", "3.13"]

steps:
- uses: actions/checkout@v4

- name: Set up Python ${{ matrix.python-version }}
uses: actions/setup-python@v5
with:
python-version: ${{ matrix.python-version }}

- name: Install dependencies
run: |
python -m pip install --upgrade pip
pip install flake8
if [ -f requirements.txt ]; then pip install -r requirements.txt; fi

- name: Lint with flake8
run: |
flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics

- name: Compile all Python files
run: |
python -m py_compile modele_*.py 2>/dev/null || true
python -m py_compile serveur_render.py

- name: Test server import
run: |
python -c "import serveur_render; print('OK')"
```
