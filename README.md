# demo-java-app-devops

**demo-java-app** est une application web développée en Java avec Spring Boot. Elle affiche une page d'accueil simple avec un titre et un message, et expose des endpoints pour la supervision. L'application utilise Thymeleaf pour le rendu HTML et peut fournir des métriques pour le monitoring.

Pipeline DevOps complète pour une application Java Spring Boot, incluant :
* ✅ CI/CD avec Jenkins
* ✅ Containerisation avec Docker
* ✅ Déploiement Kubernetes automatisé
* 🚧 Monitoring avec Prometheus/Grafana
* 🔄 Charts Helm (en développement)

## 📋 Table des matières
* [Architecture](#architecture)
* [Prérequis](#prérequis)
* [Installation](#installation)
* [Pipeline CI/CD](#pipeline-cicd)
* [Déploiement Kubernetes](#déploiement-kubernetes)
* [Monitoring](#monitoring)
* [Accès à l'application](#accès-à-lapplication)
* [Améliorations futures](#améliorations-futures)

## 🏗 Architecture

```
.
├── demo-java-app/              # Application Spring Boot
│   ├── src/                    # Code source Java
│   ├── pom.xml                 # Configuration Maven
│   └── Dockerfile              # Image Docker
├── k8s/                        # Manifests Kubernetes
│   ├── deployment.yaml         # Déploiement et Service
│   └── servicemonitor.yaml     # Configuration Prometheus
├── Jenkinsfile                 # Pipeline Jenkins
└── helm/                       # Charts Helm (à compléter)
```

## ⚙️ Prérequis

* **Jenkins** avec plugins :
   * Docker Pipeline
   * Kubernetes CLI
   * Credentials Binding
   * Git
* **Cluster Kubernetes** (MicroK8s, K3s, ou cloud)
* **Docker Hub** compte et credentials
* **kubectl** installé et configuré
* **Helm 3.x** (optionnel)

## 🚀 Installation

### 1. Cloner le dépôt
```bash
git clone https://github.com/hajarek24/demo-java-app-devops.git
cd demo-java-app-devops
```

### 2. Configurer Jenkins
```bash
# Ajouter les credentials dans Jenkins :
# - Docker Hub : REGISTRY_CREDENTIALS (username/password)
# - Kubernetes : KUBECONFIG (secret file)
```

### 3. Déployer l'infrastructure monitoring (optionnel)
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm install prometheus prometheus-community/kube-prometheus-stack
```

## 🔄 Pipeline CI/CD

### Étapes implémentées et testées ✅

1. **Checkout** : Récupération du code depuis GitHub
2. **Build** : `mvn clean package` - Construction du JAR
3. **Tests** : `mvn test` - Exécution des tests unitaires
4. **Build Docker** : Construction de l'image `hajarek24/demo-java-app:${BUILD_NUMBER}`
5. **Push Docker Hub** : Publication vers le registry Docker Hub
6. **Deploy Kubernetes** : Mise à jour automatique du déploiement

### Configuration du pipeline

Le pipeline utilise les variables d'environnement suivantes :
- `BUILD_NUMBER` : Numéro de build Jenkins pour le tag Docker
- `REGISTRY_CREDENTIALS` : Credentials Docker Hub
- `KUBECONFIG` : Configuration d'accès au cluster Kubernetes

### Dernière exécution réussie
```
✅ Build #10 - SUCCESS
- Image: hajarek24/demo-java-app:10
- Déploiement: demo-java-app configured
- Service: demo-java-app unchanged
```

## 🚢 Déploiement Kubernetes

### Ressources déployées
```yaml
# Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo-java-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: demo-java-app

# Service
apiVersion: v1
kind: Service
metadata:
  name: demo-java-app
spec:
  type: NodePort
  ports:
    - port: 8080
      nodePort: 30081
```

### Commandes utiles
```bash
# Vérifier le déploiement
kubectl get pods -l app=demo-java-app
kubectl get svc demo-java-app

# Logs de l'application
kubectl logs -l app=demo-java-app

# Scaling
kubectl scale deployment demo-java-app --replicas=3
```

## 📊 Monitoring

### Configuration Prometheus
```yaml
# ServiceMonitor pour scraping des métriques
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: demo-java-app
spec:
  selector:
    matchLabels:
      app: demo-java-app
  endpoints:
  - port: http
    path: /actuator/metrics
```

### Endpoints de supervision
- `/actuator/health` - Statut de l'application
- `/actuator/metrics` - Métriques Micrometer
- `/actuator/info` - Informations sur l'application

## 🌐 Accès à l'application

### Environnement local
- **Application** : http://localhost:30081
- **Health Check** : http://localhost:30081/actuator/health

### Environnement Jenkins  
- **Pipeline** : Jenkins → demo-java-app-test2
- **Docker Images** : [hajarek24/demo-java-app](https://hub.docker.com/r/hajarek24/demo-java-app/tags)

## 🔮 Améliorations futures

### Court terme
- [ ] Ajouter des tests unitaires et d'intégration réels
- [ ] Implémenter les charts Helm complets
- [ ] Configurer les health checks Kubernetes
- [ ] Ajouter des variables d'environnement pour la configuration

### Moyen terme  
- [ ] Pipeline multi-environnement (dev/staging/prod)
- [ ] Sécurisation avec RBAC Kubernetes
- [ ] Intégration SonarQube pour la qualité du code
- [ ] Notifications Slack/Teams sur les déploiements

### Long terme
- [ ] GitOps avec ArgoCD
- [ ] Service Mesh (Istio)
- [ ] Backup et disaster recovery
- [ ] Monitoring avancé avec Grafana dashboards

## 🏷️ Tags et versions

- **Dernière version stable** : `hajarek24/demo-java-app:10`
- **Branches** : `main` (production)
- **Convention de nommage** : `hajarek24/demo-java-app:${BUILD_NUMBER}`

## 🤝 Contribution

1. Fork le projet
2. Créer une branche feature (`git checkout -b feature/AmazingFeature`)
3. Commit les changements (`git commit -m 'Add some AmazingFeature'`)
4. Push vers la branche (`git push origin feature/AmazingFeature`)
5. Ouvrir une Pull Request

---

**Status** : ✅ **Production Ready** - Pipeline fonctionnel avec déploiement automatisé

*Dernière mise à jour : Mai 2025*

