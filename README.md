
## 📘 Проект GitOps с ArgoCD и Helm

Описание проекта: Этот репозиторий содержит инфраструктуру как код (Infrastructure as Code) и конфигурации приложения, необходимые для автоматического развертывания в Kubernetes с использованием GitOps подхода через ArgoCD и Helm чартов.
## 📌 Обзор

Тип проекта: GitOps
Оркестратор: Kubernetes
Инструмент: GitOps ArgoCD
Метод управления манифестами: Helm
Цель: Автоматическое развертывание и синхронизация состояния кластера из Git репозитория
## 🧩 Структура проекта

```
my-app/                # Пример Helm-чарта
  ├── Chart.yaml         # Описание чарта
  ├── values.yaml        # Конфигурационные параметры
  └── templates/         # Шаблоны Kubernetes манифестов
      ├── deployment.yaml
      └── service.yaml
```
## 🚀 Как использовать
1. Установка Minikube (локальный кластер)
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube start --driver=docker
```
2. Установка ArgoCD
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.8.4/manifests/install.yaml
kubectl port-forward svc/argocd-server -n argocd 8080:https
```
3. Авторизация в ArgoCD
```bash
argocd login localhost:8080 --insecure
```
### Используйте логин "admin" и пароль из:
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```
4. Создание приложения в ArgoCD (через CLI)
```bash
argocd app create my-app \
  --repo https://github.com/<ваш-логин>/gitops-demo-repo.git  \
  --path charts/my-app \
  --dest-server https://kubernetes.default.svc  \
  --dest-namespace default \
  --helm-chart my-app \
  --auto-prune \
  --sync-policy automated
```
5. Через UI ArgoCD
```
    Перейдите по ссылке: https://localhost:8080
    Нажмите + New App
    Укажите:
        Repository URL — https://github.com/<ваш-логин>/gitops-demo-repo.git
        Path — charts/my-app \
        Cluster URL — https://kubernetes.default.svc
        Namespace — default
        Sync Policy — ✅ Automated 
    Нажмите Create → Sync
```
### 🔁 Автоматическая синхронизация

Любые изменения в репозитории будут автоматически применены в кластере:
```
git add .
git commit -m "Update config"
git push origin main
```
ArgoCD обнаружит изменения и выполнит синхронизацию.

### 🛡️ Self-healing

Если состояние кластера будет изменено вручную, например:
```
kubectl scale deployment my-app --replicas=5
```
ArgoCD восстановит исходное состояние, соответствующее описанию в репозитории.
### 🔐 Безопасность

Смените начальный пароль администратора:
```
argocd account update-password
```
Для приватных репозиториев настройте SSH-ключ:
```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
Добавьте публичный ключ (~/.ssh/id_rsa.pub) в ArgoCD → Settings → Repositories

#### 📊 Мониторинг и устранение ошибок
Полезные команды:
```
argocd app list
argocd app get my-app
argocd app sync my-app
argocd app history my-app
kubectl logs -n argocd 
```
Возможные ошибки:
```
    repository not accessible	- Нет доступа к репозиторию	Проверьте права и тип репозитория 
    comparison error - Конфликт между кластером и Git	Выполните синхронизацию или проверьте манифесты 
    invalid chart path - Неверный путь до Helm чарта	Убедитесь, что структура корректна и есть Chart.yaml
```
### ✅ Что вы получаете

    Автоматизация деплоя: любое изменение в репозитории применяется в кластере.
    Self-healing: кластер всегда находится в состоянии, определённом в Git.
    История изменений: легко отслеживать и восстанавливать предыдущие версии.
    Простота масштабирования: можно добавлять новые приложения и среды (dev/staging/prod).

### 🙌 Автор

🪪 dmplastun

📧 dmitrij.plastun@gmail.com

🔗 https://github.com/dmplastun/gitops-demo-repo
