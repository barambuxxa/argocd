.
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
