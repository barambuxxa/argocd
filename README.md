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

├── demo-dev                         # Название кластера 
│   ├── applications
│   │   ├── app1.yaml                # Приложение ссылается на HelmCharts/MyChart1
│   │   └── app2.yaml                # Приложение ссылается на HelmCharts/MyChart2
│   └── root.yaml                    # Главный application в ArgoCD который разваричает все остальные приложения из папки application 
├── demo-prod
│   ├── applications
│   │   ├── app1.yaml                # Приложение ссылается на HelmCharts/MyChart1
│   │   └── app2.yaml                # Приложение ссылается на HelmCharts/MyChart2
│   └── root.yaml
├── HelmCharts                       # В этой директории содержатся helm-чарты
│   ├── MyChart1
│   │   ├── Chart.yaml               # техническую информацию
│   │   ├── templates                # содержит манифесты deploy и service, не с жёстко прописанными значениями, а с переменными
│   │   │   ├── deployment.yaml
│   │   │   └── service.yaml
│   │   ├── values_dev.yaml          # Прописано количетсво реплик
│   │   ├── values_prod.yaml         # Прописано количетсво реплик
│   │   └── values.yaml              # Прописано количетсво реплик и контейнер
│   └── MyChart2
│       ├── Chart.yaml               # техническую информацию
│       ├── templates                # содержит манифесты deploy и service, не с жёстко прописанными значениями, а с переменными
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       ├── values_dev.yaml          # Прописано количетсво реплик и контейнер
│       ├── values_prod.yaml         # Прописано количетсво реплик
│       └── values.yaml              # Прописано количетсво реплик и контейнер
└── README.md

demo-dev/root.yaml - в этом файле описано следующее: Создай в текущем кластере приложения из yaml файлов которые находятся в каталоге demo-dev/applications
Эти application Будут делаться из helmов, которые расположены HelmCharts/MyChart1 и HelmCharts/MyChart2.
В папке HelmChart расписаны все значения, для создания deployment и service.

demo-prod/root.yaml - в этом файле описано следующее: Создай в текущем кластере приложения из yaml файлов которые находятся в каталоге demo-prod/applications

После этого в веб-интерфейсе появляется данные по приложениям. Мы можем удалять оттуда и он будет автоматом запускать их основываясь на данных в гитхабе
