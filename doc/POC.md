# Демо-інструкція на отримання доступу до інтерфейсу ArgoCD

## Створення кластеру
---
### Підготовка до створення кластеру

1. Відкрити VSCode
    ![VSCode](../assets/k3d-argocd/1.png)

1. Створити контент в поточному каталозі, щоб Codespaces дозволив створити інстанс
    ![VSCode readme.md](../assets/k3d-argocd/2.png)

1. Створити репозиторій на Github
    ```
    gh repo create <username>/<repo-name> --public
    ```

1. Ініціалізувати локальний репозиторій
    ```
    git init
    ```

1. Додати файли до індексу
    ```
    git add .
    ```

1. Зробити перший коміт
    ```
    git commit -m "Initial commit"
    ```

1. Зв'язати локальний репозиторій з віддаленим
    ```
    git remote add origin https://github.com/<username>/<repo-name>
    ```

1. Відправити зміни на віддалений репозиторій
    ```
    git push -u origin main
    ```

### Створення кластеру

1. Встановити розширення для роботи з Github Codespaces
    ![Github Codespaces](../assets/k3d-argocd/3.png)

1. Створити новий Codespace
    ![Github Codespaces](../assets/k3d-argocd/4.png)

1. Вибрати репозиторій
    ![Github Codespaces](../assets/k3d-argocd/5.png)

1. Обрати стандартну гілку
    ![Github Codespaces](../assets/k3d-argocd/6.png)

1. Вибрати тип інстансу
    ![Github Codespaces](../assets/k3d-argocd/7.png)

1. Дочекатись створення інстансу
    ![Github Codespaces](../assets/k3d-argocd/8.png)
    ![Github Codespaces](../assets/k3d-argocd/9.png)

1. На Codespace встановити розширення K3D
    ![K3D extension](../assets/k3d-argocd/10.png)

1. Встановити K3D
    ![K3D](../assets/k3d-argocd/11.png)

1. Створити kubernetes кластер
    ```
    /home/codespace/.vs-kubernetes/tools/k3d/k3d cluster create <cluster-name> 
    ```
    ![K3D cluster](../assets/k3d-argocd/12.png)

### Доступ до кластеру

1. Отримати кофігурацію кластеру для kubectl
    ```
    /home/codespace/.vs-kubernetes/tools/k3d/k3d kubeconfig get <cluster-name> > config.yaml
    ```

1. В K3D розширенні натиснути на три крапки навпроти CLUSTERS і обрати "Set Kubeconfig"
    ![K3D](../assets/k3d-argocd/13.png)

1. Обрати файл з config.yaml
    ![K3D](../assets/k3d-argocd/14.png)

1. В панелі K3D з'являться елементи кластеру
    ![K3D](../assets/k3d-argocd/15.png)

---
## Встановлення та налаштування ArgoCD
---
1. Створити namespace argocd
    ```
    kubectl create namespace argocd
    ```

1. Встановити ArgoCD
    ```
    kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
    ```
    ![ArgoCD](../assets/k3d-argocd/16.png)
    ![ArgoCD](../assets/k3d-argocd/17.png)

1. Дочекатись завершення встановлення
    ![ArgoCD](../assets/k3d-argocd/18.png)

1. Отримати доступ до ArgoCD
    ```
    kubectl port-forward svc/argocd-server -n argocd 8080:443
    ```

1. Відкрити браузер та перейти за адресою http://localhost:8080
    ![ArgoCD](../assets/k3d-argocd/19.png)
    ![ArgoCD](../assets/k3d-argocd/20.png)

1. Отримати пароль для доступу до ArgoCD
    ```
    kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d; echo
    ```

1. Ввести логін admin та пароль, отриманий на попередньому кроці
    ![ArgoCD](../assets/k3d-argocd/21.png)
    ![ArgoCD](../assets/k3d-argocd/22.png)

---