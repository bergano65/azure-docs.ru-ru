---
title: Просмотр журналов и метрик с помощью Kibana и Grafana
description: Просмотр журналов и метрик с помощью Kibana и Grafana
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3693c30a34601512770f5d9071f5d786410fb00e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92360383"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Просмотр журналов и метрик с помощью Kibana и Grafana

Веб-панели мониторинга Kibana и Grafana предоставляются для получения аналитических сведений и ясности в пространствах имен Kubernetes, используемых службами данных с поддержкой Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="retrieve-the-ip-address-of-your-cluster"></a>Получение IP-адреса кластера

Для доступа к панелям мониторинга потребуется получить IP-адрес кластера. Метод получения правильного IP-адреса зависит от того, как вы решили развернуть Kubernetes. Просмотрите параметры ниже, чтобы найти нужную.

### <a name="azure-virtual-machine"></a>Виртуальная машина Azure

Используйте следующую команду, чтобы получить общедоступный IP-адрес:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

### <a name="kubeadm-cluster"></a>Кластер кубеадм

Чтобы получить IP-адрес кластера, используйте следующую команду:

```console
kubectl cluster-info
```


### <a name="aks-or-other-load-balanced-cluster"></a>AKS или другой кластер с балансировкой нагрузки

Для мониторинга среды в AKS или другом кластере с балансировкой нагрузки необходимо получить IP-адрес службы прокси-сервера управления. Используйте эту команду, чтобы получить **внешний IP-** адрес:

```console
kubectl get svc mgmtproxy-svc-external -n <namespace>

#Example:
#kubectl get svc mgmtproxy-svc-external -n arc
NAME                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)           AGE
mgmtproxy-svc-external   LoadBalancer   10.0.186.28   52.152.148.25   30777:30849/TCP   19h
```

## <a name="additional-firewall-configuration"></a>Дополнительная настройка брандмауэра

Может оказаться, что для доступа к конечным точкам Kibana и Grafana вам нужно открыть порты в брандмауэре.

Ниже приведен пример того, как это сделать для виртуальной машины Azure. Это нужно сделать, если вы развернули Kubernetes с помощью скрипта.

Ниже описаны действия по созданию правила NSG для конечных точек Kibana и Grafana.

### <a name="find-the-name-of-the-nsg"></a>Поиск имени модели NSG.

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>Добавление правила NSG

После получения имени NSG можно добавить правило с помощью следующей команды:

```azurecli
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```

## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Мониторинг управляемых экземпляров Azure SQL в службе "Дуга Azure"

Теперь, когда у вас есть IP-адрес и предоставлены порты, вы можете получить доступ к Grafana и Kibana.

> [!NOTE]
>  При появлении запроса на ввод имени пользователя и пароля введите имя пользователя и пароль, указанные во время создания контроллера данных Arc Azure.

> [!NOTE]
>  Появится сообщение с предупреждением о сертификате, поскольку сертификаты, используемые в предварительной версии, являются самозаверяющими сертификатами.

Используйте следующий шаблон URL-адреса для доступа к панелям мониторинга для ведения журнала и мониторинга для управляемого экземпляра SQL Azure:

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

Соответствующие панели мониторинга:

* "Метрики управляемого экземпляра Azure SQL"
* Метрики узла узла
* "Метрики главных модулей узлов"

## <a name="monitor-azure-database-for-postgresql-hyperscale---azure-arc"></a>Мониторинг службы "база данных Azure для PostgreSQL" в Azure Arc

Используйте следующий шаблон URL-адреса для доступа к панелям мониторинга для ведения журнала и мониторинга для PostgreSQLного масштабирования:

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

Соответствующие панели мониторинга:

* "Метрики postgres"
* "Метрики таблицы postgres"
* Метрики узла узла
* "Метрики главных модулей узлов"

## <a name="next-steps"></a>Дальнейшие шаги
- Попробуйте [передать метрики и журналы в Azure Monitor](upload-metrics-and-logs-to-azure-monitor.md)
- Дополнительные сведения о Grafana:
   - [Начало работы](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Основы Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Учебники по Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
- Дополнительные сведения о Kibana
   - [Введение](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Kibana Guide](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [Общие сведения о панели мониторинга разверткой с визуализациями данных в Kibana](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [Создание панелей мониторинга Kibana](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)

