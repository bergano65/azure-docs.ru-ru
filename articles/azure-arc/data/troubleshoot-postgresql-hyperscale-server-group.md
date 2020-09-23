---
title: Устранение неполадок с PostgreSQL масштабируемыми группами серверов
description: Устранение неполадок при PostgreSQLии групп серверов с помощью Jupyter Notebook
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 9c0d3d9c74be8dabaec20ff5d4c7e7cfc74d8eef
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939128"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>Устранение неполадок PostgreSQL в масштабируемых группах серверов

Записные книжки могут документировать процедуры, включив содержимое Markdown, чтобы описать, что нужно сделать или как это сделать. Он также может предоставлять исполняемый код для автоматизации процедуры.  Этот шаблон полезен для всех, от стандартных операционных процедур до руководств по устранению неполадок.

Например, давайте устраняем неполадки в PostgreSQLной группе серверов, которая может привести к проблемам с Azure Data Studio.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="install-tools"></a>Средства установки

Установите Azure Data Studio `kubectl` и `azdata` на клиентский компьютер, который используется для запуска записной книжки в Azure Data Studio. Для этого следуйте инструкциям в [статье Установка клиентских средств](install-client-tools.md) .

## <a name="update-the-path-environment-variable"></a>Обновление переменной среды PATH

Убедитесь, что эти средства можно вызывать из любого места на этом клиентском компьютере. Например, на клиентском компьютере Windows обновите переменную системной среды PATH и добавьте папку, в которую вы установили kubectl.

## <a name="sign-in-with-azdata"></a>Войти с помощью `azdata`

Войдите в контроллер данных ARC с этого клиентского компьютера и перед запуском Azure Data Studio. Для этого выполните следующую команду:

```console
azdata login --endpoint https://<IP address>:<port>
```

Замените `<IP address>` IP-адресом кластера Kubernetes и `<port>` портом, на котором Kubernetes прослушивается. Вам будет предложено ввести имя пользователя и пароль. Чтобы просмотреть дополнительные сведения, выполните команду: _.

```console
azdata login --help
```

## <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>Войдите в кластер Kubernetes с помощью kubectl.

Для этого может потребоваться использовать примеры команд, приведенные в [этой](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/) записи блога.
Выполните такие команды, как:

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

### <a name="the-troubleshooting-notebook"></a>Записная книжка для устранения неполадок

Запустите Azure Data Studio и откройте записную книжку устранение неполадок. 

Реализуйте шаги, описанные в разделе  [033-Manage-postgres-with-AzureDataStudio.md](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) to:

1. Подключение к контроллеру данных Arc
2. Щелкните правой кнопкой мыши экземпляр postgres и выберите **[Управление]** .
3. Выберите **панель мониторинга [диагностика и устранение проблем]**
4. Выберите **ссылку [Устранение неполадок]** .

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio — Открытие записной книжки для устранения неполадок PostgreSQL":::

Откроется **Записная книжка "TSG100" в службе "Дуга Azure с поддержкой PostgreSQL** " Azure Data Studio. Использование записной книжки для устранения :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="неполадок PostgreSQL":::

### <a name="run-the-scripts"></a>Выполнение сценариев
Нажмите кнопку "запустить все" в верхней части, чтобы запустить записную книжку одновременно, или пошаговое выполнение каждой ячейки кода по одной.

Просмотр результатов выполнения ячеек кода для любых потенциальных проблем.

Мы добавим дополнительные сведения в записную книжку, чтобы узнать, как распознать распространенные проблемы и как их решить.

## <a name="next-step"></a>Дальнейшие действия
- Ознакомьтесь со сведениями о [получении журналов для служб данных с поддержкой ARC в Azure](troubleshooting-get-logs.md) .
- Прочитайте о [поиске журналов с помощью Kibana](monitor-grafana-kibana.md)
- Сведения о [мониторинге с помощью Grafana](monitor-grafana-kibana.md)
- Создание собственных записных книжек
