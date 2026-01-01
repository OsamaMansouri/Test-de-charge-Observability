# Test de Charge et Observabilité

Projet de démonstration de test de charge et observabilité dans une architecture microservices avec gestion de concurrence.

## Description

Ce projet illustre la gestion de la concurrence dans un système de réservation de livres avec plusieurs instances de service. Il démontre l'utilisation de verrous de base de données (SELECT FOR UPDATE) pour éviter les conditions de course lors d'emprunts concurrents.

## Technologies

- **Java** (Spring Boot)
- **MySQL** 8.4
- **Docker** & Docker Compose
- **Spring Boot Actuator** (pour l'observabilité)
- **Resilience4j** (circuit breaker, retry)

## Architecture

- **book-service** : Service de gestion des livres (3 instances sur ports 8081, 8083, 8084)
- **pricing-service** : Service de calcul de prix (port 8082)
- **MySQL** : Base de données (port 3306)

## Installation

### Prérequis

- Docker et Docker Compose
- Git

### Démarrage

1. Cloner le repository :
```bash
git clone https://github.com/OsamaMansouri/Test-de-charge-Observability.git
cd Test-de-charge-Observability
```

2. Démarrer les services :
```bash
docker compose up -d --build
```

3. Vérifier que tous les services sont UP :
```bash
curl -s http://localhost:8082/actuator/health
curl -s http://localhost:8081/actuator/health
curl -s http://localhost:8083/actuator/health
curl -s http://localhost:8084/actuator/health
```

Chaque commande doit renvoyer `{"status":"UP"}`.

## Utilisation

### Créer un livre

```bash
curl -X POST http://localhost:8081/api/books \
  -H "Content-Type: application/json" \
  -d '{"title":"Test Book","author":"Author Name","stock":10}'
```

### Emprunter un livre

```bash
curl -X POST http://localhost:8081/api/books/1/borrow
```

### Test de charge

Utiliser les scripts fournis pour tester la concurrence :

**Linux/Mac :**
```bash
./loadtest.sh [BOOK_ID] [REQUESTS]
```

**Windows :**
```powershell
.\loadtest.ps1 -BookId 1 -Requests 50
```

Les requêtes sont réparties sur les 3 instances (8081, 8083, 8084).

## Structure du projet

```
.
├── book-service/          # Service de gestion des livres
├── pricing-service/       # Service de calcul de prix
├── docker-compose.yml     # Configuration Docker
├── loadtest.sh           # Script de test de charge (Linux/Mac)
└── loadtest.ps1          # Script de test de charge (Windows)
```

## Observabilité

Les endpoints Actuator sont exposés pour le monitoring :
- `/actuator/health` : État de santé
- `/actuator/metrics` : Métriques
- `/actuator/info` : Informations

## Fonctionnalités

- Gestion de la concurrence avec verrous MySQL (`SELECT FOR UPDATE`)
- Circuit breaker et retry avec Resilience4j
- Tests de charge avec distribution sur plusieurs instances
- Observabilité avec Spring Boot Actuator
