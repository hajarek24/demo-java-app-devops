# demo-java-app-devops

Pipeline DevOps complète pour une application Java Spring Boot, incluant :
- CI/CD avec Jenkins
- Déploiement Kubernetes avec Helm
- Monitoring avec Prometheus/Grafana
- Containerisation avec Docker

## 📋 Table des matières
- [Architecture](#-architecture)  
- [Prérequis](#-prérequis)  
- [Installation](#-installation)  
- [Pipeline CI/CD](#-pipeline-cicd)  
- [Monitoring](#-monitoring)  
- [Déploiement Kubernetes](#-déploiement-kubernetes)  
- [Améliorations futures](#-améliorations-futures)  

## 🏗 Architecture

.
├── demo-java-app/          # Application Spring Boot  
├── k8s/  
│   ├── deployment.yaml     # Déploiement Kubernetes  
│   └── servicemonitor.yaml # Configuration Prometheus  
├── Jenkinsfile            # Pipeline Jenkins  
└── helm/                  # Charts Helm (à compléter)  

## ⚙️ Prérequis

- Jenkins avec plugins :
  - Docker Pipeline
  - Kubernetes
  - Credentials Binding
- Cluster Kubernetes (MicroK8s)
- Docker Hub compte
- Helm 3.x

## 🚀 Installation

1. **Cloner le dépôt** :
   git clone https://github.com/hajarek24/demo-java-app-devops.git  

2. **Configurer Jenkins** :
   - Ajouter les credentials Docker Hub dans Jenkins
   - Configurer l'accès au cluster Kubernetes (kubeconfig)

3. **Déployer l'infrastructure monitoring** :  
   helm repo add prometheus-community https://prometheus-community.github.io/helm-charts  
   helm install prometheus prometheus-community/kube-prometheus-stack  

## 🔄 Pipeline CI/CD  

### Étapes implémentées :  
1. **Build** : mvn clean package  
2. **Tests** : mvn test *(à compléter avec de vrais tests)*  
3. **Build Docker** : docker build -t hajarek24/demo-java-app:${BUILD_NUMBER}  
4. **Push vers Docker Hub** *(partiellement fonctionnel)*  
5. **Déploiement Kubernetes** : kubectl apply -f k8s/deployment.yaml  

### Accès :  
- Application : http://localhost:30081  
- Jenkins : http://jenkins-url/job/demo-java-app-test  

## 📊 Monitoring  

### Composants déployés :  
- **Prometheus** : http://localhost:9090  
  - Collecte les métriques via /actuator/prometheus  
- **Grafana** : http://localhost:3000  
  - Dashboard ID: 4701    

### Métriques exposées :  
- CPU/Mémoire du pod  
- Requêtes HTTP  
- Santé de l'application (Spring Boot Actuator)  

## ☸️ Déploiement Kubernetes  

### Fichiers de configuration :  
- deployment.yaml : Déploie l'application avec 1 replica  
- servicemonitor.yaml : Configure la collecte de métriques  

Commandes manuelles :  
kubectl apply -f k8s/deployment.yaml  
kubectl apply -f k8s/servicemonitor.yaml  

## 🔧 Problèmes connus  

1. **Docker dans Jenkins** :   
   - Erreur "docker not found" dans la pipeline  
   - *Solution* : Utiliser un agent avec Docker préinstallé  

2. **Tests manquants** :  
   - Aucun test unitaire/d'intégration existant  
   - *Solution* : Implémenter des tests JUnit  

3. **Helm non documenté** :  
   - Charts Helm créés mais non versionnés  
   - *Solution* : Compléter le dossier helm/  

## 🚀 Améliorations futures  

### Priorités :  
- [ ] Compléter la configuration Helm 
- [ ] Implémenter des vrais tests automatisés  
- [ ] Ajouter SonarQube pour l'analyse de code  
- [ ] Mettre en place le blue/green deployment  

### Évolutions :  
- [ ] Auto-scaling (HPA)  
- [ ] Backup des données Prometheus  
- [ ] Sécurité : Network Policies, Pod Security Policies  

## 📚 Ressources  

- [Jenkinsfile documentation](https://www.jenkins.io/doc/book/pipeline/syntax/)  
- [Kubernetes Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)  
- [Spring Boot Actuator](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html)  

---

### Points clés à retenir :  

1. **Ce que vous avez accompli** :  
   - Pipeline Jenkins basique fonctionnelle  
   - Déploiement Kubernetes opérationnel  
   - Monitoring avec Prometheus/Grafana  
   - Intégration Docker Hub  

2. **Ce qui manque** :  
   - **Tests** : L'absence de vrais tests limite la valeur de votre CI  
   - **Helm** : Bien que utilisé, il n'est pas documenté/versionné  
   - **Resilience** : Pas de stratégie de rollback/auto-healing  

3. **Prochaines étapes immédiates** :  
   - Résoudre l'erreur Docker dans Jenkins (agent avec Docker)  
   - Ajouter au moins 2-3 tests JUnit de démonstration  
   - Documenter votre chart Helm dans /helm  

