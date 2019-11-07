---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/03/2019
ms.author: orspodek
ms.openlocfilehash: f8b3e541dfd55bbd613af2791329a08402cf8670
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581969"
---
## <a name="configure-the-data-source"></a>Настройка источника данных

Чтобы настроить обозреватель данных Azure в качестве источника данных для средства панели мониторинга, выполните следующие действия. Мы подробнее рассмотрим эти действия далее в этом разделе.

1. Создайте субъект-службу Azure Active Directory. Субъект-служба используется средством панели мониторинга для доступа к службе обозреватель данных Azure.

1. Назначьте субъекту-службе AAD роль *читателя* базы данных Azure Data Explorer.

1. Укажите свойства подключения средства панели мониторинга на основе данных от субъекта-службы Azure AD, а затем проверьте подключение.

### <a name="create-a-service-principal"></a>Создание субъекта-службы

Вы можете создать субъект-службу на [портале Azure](#azure-portal) или с помощью интерфейса командной строки [Azure CLI](#azure-cli). Независимо от выбранного метода после создания вы получите значения для четырех свойств подключения, которые потребуются вам на последующих шагах.

#### <a name="azure-portal"></a>Портал Azure

1. Чтобы создать субъект-службу, выполните инструкции, приведенные в [документации по порталу Azure](/azure/active-directory/develop/howto-create-service-principal-portal).

    1. В разделе [Назначение приложению роли](/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) присвойте роль с типом **Читатель** используемому кластеру Azure Data Explorer.

    1. В разделе [Получение значений для входа](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) скопируйте три значения свойств, описанные в шагах: **идентификатор каталога** (идентификатор клиента), **идентификатор приложения**и **пароль**.

1. На портале Azure выберите раздел **Подписки** и скопируйте идентификатор подписки, в которой вы создали субъект-службу.

    ![Идентификатор подписки — портал](media/data-explorer-configure-data-source/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure CLI

1. Создание субъекта-службы. Задайте нужную область и тип роли `reader`.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourDashboard}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Дополнительные сведения см. в статье [Создание субъекта-службы Azure с помощью Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Эта команда возвращает результаты в показанном ниже формате. Скопируйте значения трех свойств: **appID** (идентификатор приложения), **password** (пароль) и **tenant** (арендатор).


    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourDashboard}:{PortNumber}",
      "name": "https://{UrlToYourDashboard}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Получите список своих подписок.

    ```azurecli
    az account list --output table
    ```

    Скопируйте идентификатор соответствующей подписки.

    ![Идентификатор подписки — CLI](media/data-explorer-configure-data-source/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Назначение субъекту-службе роли читателя

Назначьте созданному субъекту-службе роль *читателя* базы данных Azure Data Explorer. Это действие можно выполнить в разделе **Разрешения** на портале Azure или в разделе **Запрос** с помощью команды управления.

#### <a name="azure-portal---permissions"></a>Портал Azure — разрешения

1. На портале Azure перейдите к кластеру Azure Data Explorer.

1. В разделе **Обзор** выберите базу данных с демонстрационными данными StormEvents.

    ![Выбор базы данных](media/data-explorer-configure-data-source/select-database.png)

1. Выберите **Разрешения** и щелкните **Добавить**.

    ![Разрешения базы данных](media/data-explorer-configure-data-source/database-permissions.png)

1. В разделе **Добавить разрешения базы данных** выберите роль **Читатель**, а затем щелкните **Select principals** (Выбрать субъекты).

    ![Добавление разрешений базы данных](media/data-explorer-configure-data-source/add-permission.png)

1. Найдите созданный субъект-службу. Установите флажок возле субъекта и нажмите кнопку **Выбрать**.

    ![Управление разрешениями на портале Azure](media/data-explorer-configure-data-source/new-principals.png)

1. Щелкните **Сохранить**.

    ![Управление разрешениями на портале Azure](media/data-explorer-configure-data-source/save-permission.png)

#### <a name="management-command---query"></a>Команда управления — запрос

1. На портале Azure перейдите к кластеру Azure Data Explorer и выберите **Запрос**.

    ![Запрос](media/data-explorer-configure-data-source/query.png)

1. Выполните приведенную ниже команду в окне запроса. Используйте идентификаторы приложения и арендатора, полученные с помощью портала Azure или интерфейса командной строки.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    Эта команда возвращает результаты в показанном ниже формате. В этом примере первая строка обозначает существующего пользователя в базе данных, а вторая — только что добавленный субъект-службу.

    ![Результирующий набор](media/data-explorer-configure-data-source/result-set.png)
