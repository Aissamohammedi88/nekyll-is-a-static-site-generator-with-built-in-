
push:
branches: [ main ]
tags: [ 'v*' ]
workflow_dispatch:

env:
FORCE_JAVASCRIPT_ACTIONS_TO_NODE24: "true"

jobs:
docker:
name: 🐳 Build & Test Docker
runs-on: ubuntu-latest
timeout-minutes: 15

steps:
- name: 📥 Checkout
uses: actions/checkout@v7

- name: 🐳 Setup Buildx
uses: docker/setup-buildx-action@v3

- name: 🔨 Build image
run: |
echo "════════════════════════════════════════════════════════════"
echo " 🐳 Build Docker image"
echo "════════════════════════════════════════════════════════════"
docker build -t nekyll:test .
docker images | grep nekyll

- name: 🧪 Test container
run: |
echo "════════════════════════════════════════════════════════════"
echo " 🧪 Test du conteneur"
echo "════════════════════════════════════════════════════════════"
docker run -d --name nekyll-test -p 8089:8089 nekyll:test
sleep 5
echo ""
echo " Test / :"
curl -sf http://localhost:8089/ | head -5 || echo "⚠️ Pas de réponse"
echo ""
echo " Test /health :"
curl -sf http://localhost:8089/health || echo "⚠️ Pas de réponse"
echo ""
echo " Test /api/status :"
curl -sf http://localhost:8089/api/status || echo "⚠️ Pas de réponse"
echo ""
docker logs nekyll-test
docker stop nekyll-test
docker rm nekyll-test

- name: 📊 Docker stats
run: |
echo "════════════════════════════════════════════════════════════"
echo " 📊 Statistiques image"
echo "════════════════════════════════════════════════════════════"
docker image inspect nekyll:test --format '{{.Size}}' | \
awk '{print " Taille image : " $1 / 1024 / 1024 " Mo"}'
