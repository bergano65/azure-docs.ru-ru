---
title: Визуализация данных из Azure Data Explorer в Grafana
description: Из этого практического руководства вы узнаете, как настроить Azure Data Explorer в качестве источника данных для Grafana и как визуализировать данные из примера кластера.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/05/2018
ms.openlocfilehash: c77475c052ab3ce74563aba6e4c75a50c7e41bcc
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758262"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Визуализация данных из Azure Data Explorer в Grafana

Аналитическая платформа Grafana позволяет запрашивать и визуализировать данные, а также создавать и совместно использовать панели мониторинга, основанные на этих визуализациях. Grafana предоставляет *подключаемый модуль* Azure Data Explorer, который позволяет подключиться к службе Azure Data Explorer для визуализации данных. Из этой статьи вы узнаете, как настроить Azure Data Explorer в качестве источника данных для Grafana и как визуализировать данные из примера кластера.

## <a name="prerequisites"></a>Технические условия

Для работы с этим практическим руководством необходимо следующее.

* [Grafana 5.3.0 или более поздней версии](https://docs.grafana.org/installation/) для вашей операционной системы.

* [Подключаемый модуль Azure Data Explorer](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) для Grafana.

* Кластер с демонстрационными данными StormEvents. Дополнительные сведения см. в статьях [Краткое руководство. Создание кластера и базы данных обозревателя данных Azure](create-cluster-database-portal.md) и [Прием демонстрационных данных в обозреватель данных Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-data-source"></a>Настройка источника данных

Чтобы настроить Azure Data Explorer в качестве источника данных для Grafana, с выполните приведенные ниже действия. Мы подробнее рассмотрим эти действия далее в этом разделе.

1. Создайте субъект-службу Azure Active Directory. Субъект-служба используется в Grafana для доступа к службе Azure Data Explorer.

1. Назначьте субъекту-службе AAD роль *читателя* базы данных Azure Data Explorer.

1. Укажите свойства подключения Grafana, основанные на информации от субъекта-службы Azure AD, а затем проверьте подключение.

### <a name="create-a-service-principal"></a>Создание субъекта-службы

Вы можете создать субъект-службу на [портале Azure](#azure-portal) или с помощью интерфейса командной строки [Azure CLI](#azure-cli). Независимо от выбранного метода после создания вы получите значения для четырех свойств подключения, которые потребуются вам на последующих шагах.

#### <a name="azure-portal"></a>Портал Azure

1. Чтобы создать субъект-службу, выполните инструкции, приведенные в [документации по порталу Azure](/azure/active-directory/develop/howto-create-service-principal-portal).

    1. В разделе [Назначение приложению роли](/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) присвойте роль с типом **Читатель** используемому кластеру Azure Data Explorer.

    1. В разделе [Получение значений для входа](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) скопируйте три значения свойств, описанные в инструкциях: **идентификатор каталога** (идентификатор арендатора), **идентификатор приложения** и **пароль**.

1. На портале Azure выберите раздел **Подписки** и скопируйте идентификатор подписки, в которой вы создали субъект-службу.

    ![Идентификатор подписки — портал](media/grafana/subscription-id-portal.png)

#### <a name="azure-cli"></a>Инфраструктура CLI Azure

1. Создание субъекта-службы. Задайте нужную область и тип роли `reader`.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourGrafana}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Дополнительные сведения см. в статье [Создание субъекта-службы Azure с помощью Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Эта команда возвращает результаты в показанном ниже формате. Скопируйте значения трех свойств: **appID** (идентификатор приложения), **password** (пароль) и **tenant** (арендатор).

    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourGrafana}:{PortNumber}",
      "name": "https://{UrlToYourGrafana}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Получите список своих подписок.

    ```azurecli
    az account list --output table
    ```

    Скопируйте идентификатор соответствующей подписки.

    ![Идентификатор подписки — CLI](media/grafana/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Назначение субъекту-службе роли читателя

Назначьте созданному субъекту-службе роль *читателя* базы данных Azure Data Explorer. Это действие можно выполнить в разделе **Разрешения** на портале Azure или в разделе **Запрос** с помощью команды управления.

#### <a name="azure-portal---permissions"></a>Портал Azure — разрешения

1. На портале Azure перейдите к кластеру Azure Data Explorer.

1. В разделе **Обзор** выберите базу данных с демонстрационными данными StormEvents.

    ![Выбор базы данных](media/grafana/select-database.png)

1. Выберите **Разрешения** и щелкните **Добавить**.

    ![Разрешения базы данных](media/grafana/database-permissions.png)

1. В разделе **Добавить разрешения базы данных** выберите роль **Читатель**, а затем щелкните **Select principals** (Выбрать субъекты).

    ![Добавление разрешений базы данных](media/grafana/add-permission.png)

1. Найдите созданный субъект-службу (в нашем примере это субъект **mb-grafana**). Установите флажок возле субъекта и нажмите кнопку **Выбрать**.

    ![Управление разрешениями на портале Azure](media/grafana/new-principals.png)

1. Щелкните **Сохранить**.

    ![Управление разрешениями на портале Azure](media/grafana/save-permission.png)

#### <a name="management-command---query"></a>Команда управления — запрос

1. На портале Azure перейдите к кластеру Azure Data Explorer и выберите **Запрос**.

    ![Запрос](media/grafana/query.png)

1. Выполните приведенную ниже команду в окне запроса. Используйте идентификаторы приложения и арендатора, полученные с помощью портала Azure или интерфейса командной строки.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    Эта команда возвращает результаты в показанном ниже формате. В этом примере первая строка обозначает существующего пользователя в базе данных, а вторая — только что добавленный субъект-службу.

    ![Результирующий набор](media/grafana/result-set.png)

### <a name="specify-properties-and-test-the-connection"></a>Настройка свойств и тестирование подключения

Теперь, когда вы назначили субъекту-службе роль *читателя*, можно настроить свойства для экземпляра Grafana и проверить подключение в Azure Data Explorer.

1. Откройте Grafana и щелкните значок шестеренки в меню слева, затем выберите **Data Sources** (Источники данных).

    ![Источники данных](media/grafana/data-sources.png)

1. Выберите **Add data source**(Добавить источник данных).

1. На странице **Data Sources / New** (Источники данных / Создать) введите имя для источника данных и выберите тип **Azure Data Explorer Datasource** (Источник данных Azure Data Explorer).

    ![Имя и тип подключения](media/grafana/connection-name-type.png)

1. Введите имя кластера в формате https://{имя_кластера}.{регион}.kusto.windows.net. Введите остальные значения, полученные с помощью портала Azure или интерфейса командной строки. Сопоставление параметров см. в таблице ниже.

    ![Свойства подключения](media/grafana/connection-properties.png)

    | Пользовательский интерфейс Grafana | Портал Azure | Инфраструктура CLI Azure |
    | --- | --- | --- |
    | идентификатор подписки; | Идентификатор подписки | SubscriptionId |
    | Идентификатор клиента | Идентификатор каталога | tenant |
    | Идентификатор клиента | Идентификатор приложения | appId |
    | Секрет клиента | Пароль | password |
    | | | |

1. Щелкните **Save & Test** (Сохранить и протестировать).

    Если тест завершится успешно, переходите к следующему разделу. Если возникнут проблемы, проверьте указанные в Grafana значения и повторите предыдущие шаги.

## <a name="visualize-data"></a>Визуализируйте данные

Итак, вы завершили настройку Azure Data Explorer в качестве источника данных для Grafana, и теперь пора визуализировать данные. Мы покажем здесь простой пример, но возможностей намного больше. Рекомендуем просмотреть примеры других запросов к примеру набора данных в статье [Написание запросов для обозревателя данных Azure](write-queries.md).

1. Откройте Grafana и щелкните значок плюса в меню слева, затем выберите **Dashboard** (Панель мониторинга).

    ![Создание панели мониторинга](media/grafana/create-dashboard.png)

1. На вкладке **Add** (Добавить) выберите **Graph** (График).

    ![Добавление графика](media/grafana/add-graph.png)

1. На панели графика щелкните **Panel Title** (Заголовок панели), а затем **Edit** (Редактировать).

    ![Изменение панели](media/grafana/edit-panel.png)

1. В нижней части панели щелкните **Data Source** (Источник данных) и выберите только что настроенный источник данных.

    ![Выберите источник данных](media/grafana/select-data-source.png)

1. Скопируйте приведенный ниже запрос, вставьте его в область запроса и щелкните **Run** (Запуск). Этот запрос возвращает число событий из примера набора данных в разбивке по дням.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Выполнение запроса](media/grafana/run-query.png)

1. На этом графике не отображаются никакие результаты, так как по умолчанию область действия ограничивается шестью последними часами. В меню вверху выберите **Last 6 hours** (Последние 6 часов).

    ![Последние шесть часов](media/grafana/last-six-hours.png)

1. Укажите настраиваемый диапазон, куда входит 2007 год, данные за который представлены в примере набора данных StormEvents. Нажмите кнопку **Применить**.

    ![Настраиваемый диапазон дат](media/grafana/custom-date-range.png)

    Теперь на графике отображаются данные за 2007 год по дням.

    ![Готовый график](media/grafana/finished-graph.png)

1. В меню вверху щелкните значок сохранения. ![Значок "Сохранить"](media/grafana/save-icon.png).

## <a name="next-steps"></a>Дальнейшие действия

[Написание запросов для обозревателя данных Azure](write-queries.md)

[Руководство. Визуализация данных из обозревателя данных Azure в Power BI](visualize-power-bi.md)