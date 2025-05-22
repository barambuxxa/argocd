ArgoCD. Что за зверь и для чего он нужен? Вещь очень полезная в использовании CI/CD. Основная его задача "Слушать" репозиторий. Он наблюдает либо за репозиторием или за папкой в репозитории.
Мы делаем изменения в gitHub, допустим меняем в манифесте image или количество реплик. ArgoCD видит это и делает изменения в нашем основном кластере. Так что он может видеть изменения в helme и тоже перезапускать deploymentы.
У него есть web-интерфейс.
Его можно подключить к другим ArgoCD
Чаще всего используется 1 ArgoCD для кластера 

Ну вещь прям совсем крутая. Как устанавливается:
1. Создание namespace для ArgoCD:
kubectl create namespace argocd

2. Установка ArgoCD с помощью официального манифеста
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

3. Вывешиваем веб-морду на порт 8080
kubectl port-forward svc/argocd-server -n argocd 8080:443

4. Логин admin, а вот пароль надо расшифровать:
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

Всё готово, мы зашли на веб-морду

А теперь настройка ArgoCD:
1. Прописываем путь до нужного нам репозитория
2. Настраиваем SSH подключение к нужному репозиторию на GitHub. На GitHub добавляем public ключ, а в ArgoCD private key
3. Готово

Пример из урока по ArgoCD.

Дано:
2 кластера prod и dev. В каждом кластере по 3 nodes. 

Вот сами файлы в репозитории:
├── demo-dev
│   ├── applications
│   │   ├── app1.yaml
│   │   └── app2.yaml
│   └── root.yaml
├── demo-prod
│   ├── applications
│   │   ├── app1.yaml
│   │   └── app2.yaml
│   └── root.yaml
├── HelmCharts
│   ├── MyChart1
│   │   ├── Chart.yaml
│   │   ├── templates
│   │   │   ├── deployment.yaml
│   │   │   └── service.yaml
│   │   ├── values_dev.yaml
│   │   ├── values_prod.yaml
│   │   └── values.yaml
│   └── MyChart2
│       ├── Chart.yaml
│       ├── templates
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       ├── values_dev.yaml
│       ├── values_prod.yaml
│       └── values.yaml
└── README.md
