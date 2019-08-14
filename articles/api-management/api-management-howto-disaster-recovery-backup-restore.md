---
title: Внедрение аварийного восстановления с помощью функций архивации и восстановления в службе управления API Azure | Документация Майкрософт
description: Использование архивации и восстановления для выполнения аварийного восстановления в службе управления API Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2019
ms.author: apimpm
ms.openlocfilehash: bde4572ec72286be7d845f4e83bf9c0fe3bff6f1
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932392"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Реализация аварийного восстановления с помощью функций резервного копирования и восстановления службы в Azure API Management

Опубликовав интерфейсы API и управляя ими с помощью службы управления API Azure, вы получаете возможности по обеспечению отказоустойчивости и организации инфраструктуры, которые в противном случае вам пришлось бы проектировать и внедрять вручную. Платформа Azure устраняет большую часть потенциальных сбоев при относительно небольших затратах.

Чтобы обеспечить восстановление в случае проблем с доступностью в регионе, в котором размещена ваша служба управления API, будьте готовы в любой момент создать ее в другом регионе. В зависимости от цели времени восстановления может потребоваться сохранение резервной службы в одном или нескольких регионах. Вы также можете попытаться сохранить конфигурацию и содержимое в синхронизации с активной службой в соответствии с целевой точкой восстановления. Функции резервного копирования и восстановления службы предоставляют необходимые стандартные блоки для реализации стратегии аварийного восстановления.

Операции резервного копирования и восстановления также можно использовать для репликации конфигурации службы управления API между операционными средами, например для разработки и промежуточного хранения. Помните, что данные среды выполнения, такие как пользователи и подписки, также будут копироваться, что может быть не всегда желательным.

В этом руководстве показано, как автоматизировать операции резервного копирования и восстановления, а также как обеспечить успешную проверку подлинности запросов на резервное копирование и восстановление, Azure Resource Manager.

> [!IMPORTANT]
> Операция восстановления не изменяет конфигурацию настраиваемого узла целевой службы. Мы рекомендуем использовать одно и то же настраиваемое имя узла и сертификат TLS как для активных, так и для резервных служб, чтобы после завершения операции восстановления трафик можно было перенаправлять в резервный экземпляр с помощью простого изменения DNS CNAME.
>
> Операция резервного копирования не захватывает предварительно агрегированные данные журнала, используемые в отчетах, отображаемых в колонке аналитики в портал Azure.

> [!WARNING]
> Срок действия каждой резервной копии истекает через 30 дней. Если вы попытаетесь восстановить резервную копию по истечении 30 дней, восстановление завершится сбоем и будет отображено сообщение `Cannot restore: backup expired`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Проверка подлинности запросов к диспетчеру ресурсов Azure

> [!IMPORTANT]
> Интерфейс REST API для резервного копирования и восстановления использует диспетчер ресурсов Azure и применяет механизм проверки подлинности, отличный от интерфейсов REST API, используемых для управления объектами службы управления API. В этом разделе описываются действия, необходимые для проверки подлинности запросов к диспетчеру ресурсов Azure. Дополнительные сведения см. в [справочнике REST API Azure](/rest/api/index).

Все задачи, которые выполняются в ресурсах с помощью Azure Resource Manager, необходимо аутентифицировать в Azure Active Directory. Для этого:

-   добавьте приложение в клиент Azure Active Directory;
-   настройте разрешения для добавленного приложения;
-   получите маркер для проверки подлинности запросов к диспетчеру ресурсов Azure.

### <a name="create-an-azure-active-directory-application"></a>Создание приложения Azure Active Directory

1. Войдите на [портале Azure](https://portal.azure.com).
2. Используя подписку, включающую экземпляр службы управления API, перейдите на вкладку **Регистрация приложений** в **Azure Active Directory** (Azure Active Directory > Управление/Регистрация приложений).

    > [!NOTE]
    > Если этот каталог не отображается в вашей учетной записи, обратитесь к администратору подписки Azure, чтобы получить необходимые разрешения для учетной записи.

3. Щелкните **Регистрация нового приложения**.

    Справа появится окно **Создание**. Здесь вам нужно ввести информацию о приложении AAD.

4. Введите имя приложения.
5. Выберите тип приложения **Машинный код**.
6. Введите в поле **URI перенаправления** любой URL-адрес, например `http://resources`. Это поле является обязательным, но введенное значение впоследствии не используется. Установите флажок, чтобы сохранить приложение.
7. Нажмите кнопку **Создать**.

### <a name="add-an-application"></a>Добавить приложение

1. После создания приложения щелкните **Параметры**.
2. Теперь щелкните **Требуемые разрешения**.
3. Щелкните **+Добавить**.
4. Нажмите кнопку **Выбор API**.
5. Выберите **Windows** **Azure Service Management API** (API управления службами Azure).
6. Нажмите кнопку **Выбрать**.

    ![Добавление разрешений](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Щелкните **Делегированные разрешения** рядом с только что добавленным приложением и установите флажок **Access Azure Service Management (preview)** (Доступ к управлению службами Azure (предварительная версия)).
8. Нажмите кнопку **Выбрать**.
9. Щелкните **Предоставить разрешение**

### <a name="configuring-your-app"></a>Настройка приложения

Прежде чем вызывать интерфейсы API, выполняющие резервное копирование и восстановление, необходимо получить маркер. В следующем примере для получения маркера используется пакет NuGet [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory).

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Замените `{tenant id}`, `{application id}` и `{redirect uri}`, следуя приведенным инструкциям.

1. Замените `{tenant id}` идентификатором клиента для созданного приложения Azure Active Directory. Чтобы узнать этот идентификатор, выберите **Регистрация приложений** -> **Конечные точки**.

    ![Конечные точки][api-management-endpoint]

2. Замените `{application id}` значением, которое представлено на странице **Параметры**.
3. Замените значение `{redirect uri}` на значение с вкладки **URI перенаправления** приложения Azure Active Directory.

    Когда все значения будут указаны, этот пример кода должен вернуть примерно такой маркер:

    ![Токен][api-management-arm-token]

    > [!NOTE]
    > Срок действия маркера может истечь после определенного периода. Выполнить образец кода снова, чтобы создать новый маркер.

## <a name="calling-the-backup-and-restore-operations"></a>Вызов операций резервного копирования и восстановления

Интерфейсы REST API: [служба управления API — резервное копирование](/rest/api/apimanagement/2019-01-01/apimanagementservice/backup) и [служба управления API — восстановление](/rest/api/apimanagement/2019-01-01/apimanagementservice/restore).

Прежде чем выполнять операции резервного копирования и восстановления, описанные в следующих разделах, задайте заголовок запроса авторизации для вызова REST.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"> </a>Архивация службы управления API

Чтобы выполнить архивацию службы управления API, отправьте следующий HTTP-запрос:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

где:

-   `subscriptionId` — идентификатор подписки, содержащей службу управления API, для которой вы пытаетесь выполнить резервное копирование;
-   `resourceGroupName` — имя группы ресурсов службы управления API Azure
-   `serviceName` — имя службы управления API, резервное копирование которой вы выполняете, на момент ее создания;
-   `api-version`-заменить на`2018-06-01-preview`

В тексте запроса укажите целевую учетную запись хранения Azure, ключ доступа, имя контейнера BLOB-объектов и имя резервной копии:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Для заголовка запроса `Content-Type` установите значение `application/json`.

Резервное копирование — это длительная операция, которая занимает несколько минут. Если запрос выполнен успешно и запущен процесс резервного копирования, вы получите код состояния ответа `202 Accepted` с заголовком `Location`. Чтобы отслеживать состояние операции, отправляйте запросы GET на URL-адрес, указанный в заголовке `Location` . Пока резервное копирование выполняется, вы получаете код состояния "202 Accepted". Код ответа `200 OK` указывает на то, что резервное копирование успешно завершено.

Отправляя запрос на резервное копирование, учитывайте следующие ограничения:

-   **Контейнер**, указанный в теле запроса, **должен существовать**.
-   Пока выполняется резервное копирование, **не вносите изменений в управление службами**, таких как повышение или понижение уровня SKU, смена доменного имени и т. д.
-   Восстановление **резервной копии гарантируется только в течение 30 дней** с момента ее создания.
-   **Данные об использовании**, применяемые при создании аналитических отчетов, **не включаются** в резервную копию. Для периодического извлечения аналитических отчетов с целью их дальнейшего помещения на хранение используйте [API REST Azure API Management][azure api management rest api] .
-   Кроме того, следующие элементы не являются частью данных резервной копии: сертификаты SSL пользовательского домена, а также любые промежуточные или корневые сертификаты, загруженные клиентом, содержимым портала разработчика и параметрами интеграции виртуальной сети.
-   Частота резервного копирования службы влияет на целевую точку восстановления. Чтобы максимально снизить этот показатель, мы советуем настроить регулярное резервное копирование, а также выполнять резервное копирование по требованию после внесения изменений в службу управления API.
-   **Изменения**, внесенные в конфигурацию службы (интерфейсы API, политики, внешний вид портала разработчика) во время резервного копирования, **могут быть исключены из резервной копии и утеряны**.
-   **Разрешить** доступ из плоскости управления к учетной записи хранения Azure. Для резервного копирования клиент должен открыть следующий набор входящих IP-адресов в своей учетной записи хранения. 
    > 13.84.189.17/32, 13.85.22.63/32, 23.96.224.175/32, 23.101.166.38/32, 52.162.110.80/32, 104.214.19.224/32, 13.64.39.16/32, 40.81.47.216/32, 51.145.179.78/32, 52.142.95.35/32, 40.90.185.46/32, 20.40.125.155/32
### <a name="step2"> </a>Восстановление службы управления API

Чтобы восстановить службу управления API из ранее созданной резервной копии, отправьте следующий HTTP-запрос:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

где:

-   `subscriptionId` — идентификатор подписки, включающей в себя службу управления API, которую нужно восстановить из резервной копии;
-   `resourceGroupName` — имя группы ресурсов, включающей в себя службу управления API Azure, в которую нужно восстановить резервную копию;
-   `serviceName` — имя восстанавливаемой службы управления API на момент ее создания;
-   `api-version`-заменить на`2018-06-01-preview`

В тексте запроса укажите расположение файла резервной копии, то есть добавьте целевую учетную запись хранения Azure, ключ доступа, имя контейнера больших двоичных объектов и имя резервной копии:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Для заголовка запроса `Content-Type` установите значение `application/json`.

Восстановление — это длительная операция, которая может занять до 30 минут и более. Если запрос выполнен успешно и процесс восстановления инициирован, вы получите код состояния ответа `202 Accepted` с заголовком `Location`. Чтобы отслеживать состояние операции, отправляйте запросы GET на URL-адрес, указанный в заголовке `Location` . Пока восстановление выполняется, вы получаете код состояния "202 Accepted". Код ответа `200 OK` указывает на то, что восстановление успешно завершено.

> [!IMPORTANT]
> **Номер SKU** восстанавливаемой службы **должен совпадать** с номером SKU, сохраненным в резервной копии.
>
> **Изменения**, внесенные в конфигурацию службы (например, интерфейсы API, политики, внешний вид портала разработчика) во время восстановления, **могут быть перезаписаны**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> Операции резервного копирования и восстановления также можно выполнять с помощью команд PowerShell _BACKUP-азапиманажемент_ и RESTORE _-азапиманажемент_ соответственно.

## <a name="next-steps"></a>Следующие шаги

Чтобы ознакомиться с другими способами резервного копирования и восстановления, ознакомьтесь со следующими ресурсами.

-   [Репликация учетных записей управления API Azure](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Automating API Management Backup and Restore with Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps) (Автоматизация управления API резервного копирования и восстановления с помощью Logic Apps)
-   В статье [Управление API Azure: резервное копирование и восстановление конфигурации](https://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
    _Стюарт подробно описывает альтернативный подход, который отличается от официальных рекомендаций, но тоже заслуживает внимания._

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
