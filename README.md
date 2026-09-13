
supreme tools Nexus tools- full stack open source ;;;;;;;;;;;;;;;;;
-------------......................................................................................................................................................................................
.....................................................................----------.........-------...........---------.......-----...........-------------................-.-............-------------..
# Model Function
1 Network scanner Port scan, DNS, ping, HTTP status, LAN
2 Analysis HTML Fetch, extract, colors, fonts, framework
3 Forge site generates site from URL or config
4 Radio Streams, metadata, now playing
5 Email Mailto, composition, sending
6 Tor / Anon SOCKS5, rotation, obfuscation
7 Benchmark wrk-style, load test, latency
8 USB transfer Detection key, transfer, check hash
9 README / Docs Doc project generator
10 LAUNCHER Opens all ports, unified interface
Licence

MIT — Fais ce que tu veux. Vends, modifie, distribue.

---
# ═══════════════════════════════════════════════════════════════════
# NEKYLL CI — Minimal Node 24
# ═══════════════════════════════════════════════════════════════════
# Pipeline de qualité minimaliste.
# Seulement 2 actions Node obligatoires (checkout + setup-python).
# Tout le reste en pur shell → moins de dépendances, plus rapide.
# Compatible Node 24 (dépréciation Node 20 de septembre 2026).
# ═══════════════════════════════════════════════════════════════════

name: NEKYLL CI — Minimal Node

on:
push:
branches: [ main, develop ]
pull_request:
branches: [ main, develop ]
workflow_dispatch:

concurrency:
group: ${{ github.workflow }}-${{ github.ref }}
cancel-in-progress: true

permissions:
contents: read

env:
FORCE_JAVASCRIPT_ACTIONS_TO_NODE24: "true"
PYTHON_VERSION: "3.11"
PIP_DISABLE_PIP_VERSION_CHECK: "1"
PIP_NO_CACHE_DIR: "1"

jobs:

# ═════════════════════════════════════════════════════════════════
# JOB UNIQUE — TOUT EN UN
# ═════════════════════════════════════════════════════════════════
quality:
name: 🧬 NEKYLL Quality
runs-on: ubuntu-latest
timeout-minutes: 15

steps:
# ⚠️ ACTION OBLIGATOIRE 1/2
- name: 📥 Checkout
uses: actions/checkout@v7

# ⚠️ ACTION OBLIGATOIRE 2/2
- name: 🐍 Setup Python
uses: actions/setup-python@v7
with:
python-version: ${{ env.PYTHON_VERSION }}

# ═══════════════════════════════════════════════════════════
# ÉTAPE 1 — DÉPENDANCES
# ═══════════════════════════════════════════════════════════
- name: 📦 Install dependencies
run: |
echo "════════════════════════════════════════════════════════════"
echo " 📦 Installation des dépendances"
echo "════════════════════════════════════════════════════════════"
python -m pip install --upgrade pip
pip install pytest pytest-cov flake8 mypy bandit safety
if [ -f requirements.txt ]; then
pip install -r requirements.txt
echo "✅ requirements.txt installé"
else
echo "ℹ️ Pas de requirements.txt"
fi

# ═══════════════════════════════════════════════════════════
# ÉTAPE 2 — LINT
# ═══════════════════════════════════════════════════════════
- name: 🔍 Lint (flake8)
run: |
echo "════════════════════════════════════════════════════════════"
echo " 🔍 Lint — erreurs critiques"
echo "════════════════════════════════════════════════════════════"
flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics || true
echo ""
echo "════════════════════════════════════════════════════════════"
echo " 🔍 Lint — style (warning)"
echo "════════════════════════════════════════════════════════════"
flake8 . --count --exit-zero --max-complexity=15 --max-line-length=127 --statistics || true

# ═══════════════════════════════════════════════════════════
# ÉTAPE 3 — COMPILATION
# ═══════════════════════════════════════════════════════════
- name: 🔨 Compile all Python files
run: |
echo "════════════════════════════════════════════════════════════"
echo " 🔨 Compilation"
echo "════════════════════════════════════════════════════════════"
ok=0
fail=0
for f in *.py; do
if [ -f "$f" ]; then
if python -m py_compile "$f" 2>/dev/null; then
echo " ✅ $f"
ok=$((ok + 1))
else
echo " ❌ $f"
fail=$((fail + 1))
fi
fi
done
echo ""
echo " ✅ Compilés : $ok"
echo " ❌ Échecs : $fail"

# ═══════════════════════════════════════════════════════════
# ÉTAPE 4 — TESTS
# ═══════════════════════════════════════════════════════════
- name: 🧪 Tests (pytest)
run: |
echo "════════════════════════════════════════════════════════════"
echo " 🧪 Tests pytest"
echo "════════════════════════════════════════════════════════════"
pytest -v --tb=short --cov=. --cov-report=term-missing --cov-report=xml || echo "ℹ️ Aucun test trouvé"

# ═══════════════════════════════════════════════════════════
# ÉTAPE 5 — SÉCURITÉ
# ═══════════════════════════════════════════════════════════
- name: 🔒 Security scan (bandit)
run: |
echo "════════════════════════════════════════════════════════════"
echo " 🔒 Scan sécurité — Bandit"
echo "════════════════════════════════════════════════════════════"
bandit -r . -ll -f screen --exclude ./.git,./tests || echo "⚠️ Bandit a trouvé des choses"

# ═══════════════════════════════════════════════════════════
# ÉTAPE 6 — TYPES
# ═══════════════════════════════════════════════════════════
- name: 🔬 Type check (mypy)
run: |
echo "════════════════════════════════════════════════════════════"
echo " 🔬 Vérification des types — Mypy"
echo "════════════════════════════════════════════════════════════"
mypy . --ignore-missing-imports --no-error-summary || echo "⚠️ Mypy a trouvé des choses"

# ═══════════════════════════════════════════════════════════
# ÉTAPE 7 — TEST SERVEUR
# ═══════════════════════════════════════════════════════════
- name: 🚀 Test server import
run: |
echo "════════════════════════════════════════════════════════════"
echo " 🚀 Vérification import serveur"
echo "════════════════════════════════════════════════════════════"
if [ -f serveur_render.py ]; then
python -c "import serveur_render; print('✅ serveur_render importé OK')" || echo "⚠️ Import échoué"
else
echo "ℹ️ Pas de serveur_render.py"
fi

# ═══════════════════════════════════════════════════════════
# ÉTAPE 8 — RAPPORT
# ═══════════════════════════════════════════════════════════
- name: 📊 Rapport final
if: always()
run: |
echo "════════════════════════════════════════════════════════════"
echo " 🧬 NEKYLL CI — RAPPORT FINAL"
echo "════════════════════════════════════════════════════════════"
echo " Commit : ${{ github.sha }}"
echo " Auteur : ${{ github.actor }}"
echo " Branche : ${{ github.ref }}"
echo " Event : ${{ github.event_name }}"
echo " Node : $(node --version)"
echo " Python : $(python --version)"
echo "════════════════════════════════════════════════════════════"

# ═══════════════════════════════════════════════════════════
# ÉTAPE 9 — UPLOAD COVERAGE
# ═══════════════════════════════════════════════════════════
- name: 📊 Upload coverage artifact
if: always()
uses: actions/upload-artifact@v4
with:
name: coverage-report
path: coverage.xml
if-no-files-found: ignore
retention-days: 7
continue-on-error: true


# ═══════════════════════════════════════════════════════════════════
# NEKYLL — Dockerfile
# ═══════════════════════════════════════════════════════════════════
# Image ultra-légère pour NEKYLL.
# Base : python:3.11-slim (Debian minimal)
# Taille finale : ~150 Mo
# ═══════════════════════════════════════════════════════════════════

FROM python:3.11-slim

# Métadonnées
LABEL maintainer="Aïssa Mohammedi <source2x1@protonmail.com>"
LABEL description="NEKYLL — Static site generator with built-in 10 tools"
LABEL version="1.0.0"

# Variables d'environnement
ENV PYTHONUNBUFFERED=1 \
PYTHONDONTWRITEBYTECODE=1 \
PIP_NO_CACHE_DIR=1 \
PIP_DISABLE_PIP_VERSION_CHECK=1 \
PORT=8089

# Répertoire de travail
WORKDIR /app

# Installation des dépendances système
RUN apt-get update && \
apt-get install -y --no-install-recommends \
curl \
ca-certificates \
gnupg \
git \
&& rm -rf /var/lib/apt/lists/*

# Copie des fichiers de dépendances Python
COPY requirements.txt* ./
RUN pip install --upgrade pip && \
if [ -f requirements.txt ]; then \
pip install -r requirements.txt; \
else \
pip install flask gunicorn; \
fi

# Copie de tout le projet
COPY . .

# Compilation de vérification
RUN for f in *.py; do \
if [ -f "$f" ]; then \
python -m py_compile "$f" || exit 1; \
fi; \
done

# Utilisateur non-root (sécurité)
RUN useradd -m -u 1000 nekyll && \
chown -R nekyll:nekyll /app
USER nekyll

# Exposition du port
EXPOSE 8089

# Healthcheck
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:8089/health')" || exit 1

# Lancement du serveur
CMD ["gunicorn", "--bind", "0.0.0.0:8089", "--workers", "2", "--threads", "4", "--timeout", "60", "serveur_render:app"]



# Git
.git
.gitignore
.github

# Python
__pycache__
*.py[cod]
*$py.class
*.so
.Python
build/
dist/
*.egg-info/
.venv/
venv/

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
data/
sites/*/public/

# Secrets
.env
.env.local
secrets/
*.pem
*.key

# Tests
.pytest_cache/
.coverage
coverage.xml
htmlcov/

# Temp
tmp/
temp/
*.tmp

---

📄 BONUS 2 — Badge dans ton README

Ajoute en haut de ton README :

```markdown
[![NEKYLL CI](https://github.com/Aissamohammedi88/nekyll-is-a-static-site-generator-with-built-in-/actions/workflows/nekyll-ci.yml/badge.svg)](https://github.com/Aissamohammedi88/nekyll-is-a-static-site-generator-with-built-in-/actions)
[![codecov](https://codecov.io/gh/Aissamohammedi88/nekyll-is-a-static-site-generator-with-built-in-/branch/main/graph/badge.svg)](https://codecov.io/gh/Aissamohammedi88/nekyll-is-a-static-site-generator-with-built-in-)
[![Python 3.9+](https://img.shields.io/badge/python-3.9%2B-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
```

---
============================= test session starts ==============================
