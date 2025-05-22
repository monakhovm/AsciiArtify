# Концепція вибору інструменту для локального Kubernetes (PoC)

## Вступ
Для локального розгортання Kubernetes кластерів стартап "AsciiArtify" розглядає три інструменти:
- **minikube** – класичне рішення для локального Kubernetes з підтримкою різних драйверів.
- **kind** – легковисний інструмент на базі Docker для створення кластерів у контейнерах.
- **k3d** – оптимізований Kubernetes (Rancher k3s) у Docker з мінімальним споживанням ресурсів.
---

## Порівняльні характеристики

| Критерій               | minikube                          | kind                              | k3d                               |
|------------------------|-----------------------------------|-----------------------------------|-----------------------------------|
| **Підтримувані ОС**    | Linux, macOS, Windows             | Linux, macOS, Windows             | Linux, macOS, Windows             |
| **Архітектури**        | AMD64, ARM (обмежено)             | AMD64, ARM                        | AMD64, ARM                        |
| **Складність встановлення** | Середня (потребує драйверів) | Низька (працює через Docker)      | Низька (одна команда)             |
| **Швидкість розгортання** | 2-5 хв                          | 1-3 хв                           | 30-60 сек                        |
| **Споживання ресурсів** | Високе (виділяє окрему ВМ)      | Середнє (контейнери Docker)       | Низьке (легкий k3s)               |
| **Моніторинг**          | Додаткові аддони (Prometheus)     | Ручне налаштування                | Вбудований Traefik/Lens           |
| **Ліцензія**           | Apache-2.0                        | Apache-2.0                        | MIT                               |
| **Підтримка Podman**   | Ні                                | Так (через Docker API)            | Так (через Docker-сумісність)     |

---

## Переваги та недоліки

### minikube
✅ **Переваги**:  
- Найкраща документація.  
- Підтримка мульти-нодових кластерів.  
- Інтеграція з Kubectl.  

❌ **Недоліки**:  
- Повільний старт.  
- Високе споживання RAM/CPU.  
- Складне налаштування мережі.

---

### kind (Kubernetes IN Docker)
✅ **Переваги**:  
- Ідеальний для CI/CD (легка інтеграція).  
- Не потребує віртуалізації.  
- Підтримка Kubernetes 1.27+.  

❌ **Недоліки**:  
- Обмежена масштабованість.  
- Відсутність вбудованого LoadBalancer.  
- Складність дебагінга.

---

### k3d (Rancher k3s)
✅ **Переваги**:  
- Найшвидший старт кластера.  
- Мінімальні ресурси (до 512 MB RAM).  
- Автоматичне створення Ingress (Traefik).  

❌ **Недоліки**:  
- Обмежена підтримка CRD.  
- Деякі компоненти Kubernetes видалені (наприклад, Cloud Provider).  

---

## Демонстрація роботи з k3d
**Рекомендація для AsciiArtify**: k3d через швидкість та низькі вимоги до ресурсів.

![k3d.gif](../assets/k3d.gif)

### Крок 1: Встановлення
```
curl -s https://raw.githubusercontent.com/k3d-io/k3d/main/install.sh | bash
```

### Крок 2: Створення кластера
```
k3d cluster create ascii-cluster
```


### Крок 3: Розгортання "Hello World"
```
kubectl create deployment hello --image=nginx
kubectl expose deployment hello --port=80 --type=NodePort
kubectl port-forward service/hello 8080:80
```


Відкрийте `http://localhost:8080` у браузері.

---
## Демонстрація роботи з kind

![k3d.gif](../assets/kind.gif)

### Крок 1: Встановлення
```
# For AMD64 / x86_64
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.28.0/kind-linux-amd64
# For ARM64
[ $(uname -m) = aarch64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.28.0/kind-linux-arm64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```

### Крок 2: Створення кластера
```
kind create cluster
```


### Крок 3: Розгортання "Hello World"
```
kubectl create deployment hello --image=nginx
kubectl expose deployment hello --port=80 --type=NodePort
kubectl port-forward service/hello 8080:80
```


Відкрийте `http://localhost:8080` у браузері.
---
## Демонстрація роботи з minikube

![k3d.gif](../assets/minikube.gif)

### Крок 1: Встановлення
```
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
```

### Крок 2: Створення кластера
```
minikube start [--driver=<docker,podman>]
```


### Крок 3: Розгортання "Hello World"
```
kubectl create deployment hello --image=nginx
kubectl expose deployment hello --port=80 --type=NodePort
kubectl port-forward service/hello 8080:80
```


Відкрийте `http://localhost:8080` у браузері.
---

## Ліцензійні ризики з Docker
- **Docker Desktop**: Для комерційних команд (>250 співробітників) потрібна платна ліцензія.  
- **Альтернатива**: Використовуйте [Podman](https://podman.io) з `podman-docker` для сумісності.  

---

## Висновки
1. **Для PoC у стартапі**:  
 - 🥇 **k3d** – оптимальний вибір через швидкість та низький поріг входження.  
 - 🥈 **kind** – варіант для інтеграції з CI/CD.  
 - 🥉 **minikube** – підходить для навчання, але не для швидкого тестування.  

2. **Рекомендації**:  
 - Використовуйте k3d для локальної розробки PoC.  
 - У випадку масштабування переходьте на managed Kubernetes (EKS, GKE).  
 - Моніторинг: Додайте Lens або K9s для візуалізації кластера.
