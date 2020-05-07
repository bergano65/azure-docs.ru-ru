---
title: Встроенные роли Azure — Azure RBAC
description: В этой статье описываются встроенные роли Azure для управления доступом на основе ролей Azure (Azure RBAC). В нем перечислены действия, неизменность, действия с Нотдатаактионс и.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/04/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: b6587ca113319c266d2335c9cbb154e0ecc4afa1
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838745"
---
# <a name="azure-built-in-roles"></a>Встроенные роли Azure

[Управление доступом на основе ролей в Azure (Azure RBAC)](overview.md) имеет несколько встроенных ролей Azure, которые можно назначить пользователям, группам, субъектам-службам и управляемым удостоверениям. Назначение ролей является способом управления доступом к ресурсам Azure. Если встроенные роли не соответствуют конкретным потребностям Организации, можно создать собственные [пользовательские роли Azure](custom-roles.md).

В этой статье перечислены встроенные роли Azure, которые постоянно развиваются. Чтобы получить последние роли, используйте команду [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) или [az role definition list](/cli/azure/role/definition#az-role-definition-list). Если вы ищете роли администратора для Azure Active Directory (Azure AD), ознакомьтесь [с разрешениями роли администратора в Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="all"></a>All

В следующей таблице приводится краткое описание и уникальный идентификатор каждой встроенной роли. `Actions`Выберите имя роли, чтобы просмотреть список `NotActions`, `DataActions`, и `NotDataActions` для каждой роли. Сведения о том, что означают эти действия и о том, как они применяются к плоскостям управления и данных, см. в разделе [сведения о определениях ролей Azure](role-definitions.md).


> [!div class="mx-tableFixed"]
> | Встроенные роли | Описание | ID |
> | --- | --- | --- |
> | **Общие** |  |  |
> | [Участник](#contributor) | Позволяет управлять всем, кроме предоставления доступа к ресурсам. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Владелец](#owner) | Позволяет управлять всем, в том числе доступом к ресурсам. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Читатель](#reader) | Может просматривать все элементы, но не вносить изменения. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Администратор доступа пользователей](#user-access-administrator) | Позволяет управлять доступом пользователей к ресурсам Azure. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Compute** |  |  |
> | [Участник классической виртуальной машины](#classic-virtual-machine-contributor) | Позволяет управлять классическими виртуальными машинами, но не доступом к ним и не учетной записью виртуальной сети или хранения, к которой они подключены. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Имя для входа администратора виртуальной машины](#virtual-machine-administrator-login) | Позволяет просматривать виртуальные машины на портале и входить в систему с правами администратора. | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Участник виртуальной машины](#virtual-machine-contributor) | Позволяет управлять виртуальными машинами, но не доступом к ним и не виртуальной сетью или учетной записью хранения, к которой они подключены. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Имя входа пользователя виртуальной машины](#virtual-machine-user-login) | Позволяет просматривать виртуальные машины на портале и входить в систему с правами обычного пользователя. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Сеть** |  |  |
> | [Участник конечных точек CDN](#cdn-endpoint-contributor) | Может управлять конечными точками CDN, но не может предоставлять доступ другим пользователям. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [Читатель конечной точки CDN](#cdn-endpoint-reader) | Может просматривать конечные точки CDN, но не может вносить изменения. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [Участник профиля CDN](#cdn-profile-contributor) | Может управлять профилями CDN и их конечными точками, но не может предоставлять доступ другим пользователям. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [Читатель данных профиля CDN](#cdn-profile-reader) | Может просматривать профили CDN и их конечные точки, но не может вносить изменения. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Участник классической сети](#classic-network-contributor) | Позволяет управлять классическими сетями, но не доступом к ним. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [Участник зоны DNS](#dns-zone-contributor) | Позволяет управлять зонами DNS и наборами записей в Azure DNS, но не тем, кому они будут доступны. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Участник сети](#network-contributor) | Позволяет управлять сетями, но не доступом к ним. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Участник диспетчера трафика](#traffic-manager-contributor) | Позволяет управлять профилями диспетчера трафика, но не доступом к ним. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Память** |  |  |
> | [Участник авере](#avere-contributor) | Может создавать кластер Авере Вфкст и управлять им. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Оператор авере](#avere-operator) | Используется кластером Авере Вфкст для управления кластером. | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Участник резервного копирования](#backup-contributor) | Позволяет управлять службой архивации, но не может создавать хранилища и предоставлять доступ другим пользователям. | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Оператор резервного копирования](#backup-operator) | Позволяет управлять службами архивации, но не удалять архивные копии, создавать хранилища или предоставлять доступ другим пользователям | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Читатель резервных копий](#backup-reader) | Может просматривать службы резервного копирования, но не может вносить изменения. | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Участник классической учетной записи хранения](#classic-storage-account-contributor) | Позволяет управлять классическими учетными записями хранения, но не предоставлять доступ к ним. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Роль службы оператора ключей классических учетных записей хранения](#classic-storage-account-key-operator-service-role) | Операторы ключей классических учетных записей хранения могут перечислять и повторно создавать ключи в классических учетных записях хранения. | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Участник Data Box](#data-box-contributor) | Позволяет управлять всеми данными службы Data Box, кроме предоставления доступа другим пользователям. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Читатель Data Box](#data-box-reader) | Позволяет управлять службой Data Box, но не позволяет создавать заказы и менять их порядок, а также предоставлять доступ другим пользователям. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Разработчик Data Lake Analytics](#data-lake-analytics-developer) | Позволяет отправлять, отслеживать задания и управлять ими, но не позволяет создавать или удалять учетные записи Data Lake Analytics. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Модуль чтения и доступ к данным](#reader-and-data-access) | Позволяет просматривать все данные, но не позволит удалить или создать учетную запись хранения или содержащий ресурс. Он также предоставит доступ на чтение и запись для всех данных, содержащихся в учетной записи хранения через доступ к ключам учетной записи хранения. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Участник учетной записи хранения](#storage-account-contributor) | Разрешает управление учетными записями хранения. Предоставляет доступ к ключу учетной записи, который можно использовать для доступа к данным через авторизацию с общим ключом. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Роль службы оператора ключей учетных записей хранения](#storage-account-key-operator-service-role) | Разрешает перечисление и повторное создание ключей доступа к учетной записи хранения. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [участник данных BLOB-объектов хранилища](#storage-blob-data-contributor); | Чтение, запись и удаление контейнеров службы хранилища Azure и больших двоичных объектов. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [владелец данных BLOB-объектов хранилища](#storage-blob-data-owner); | Предоставляет полный доступ к контейнерам и данным больших двоичных объектов службы хранилища Azure, включая назначение управления доступом POSIX. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [читатель данных больших двоичных объектов хранилища](#storage-blob-data-reader). | Прочтите и перечислите контейнеры и большие двоичные объекты службы хранилища Azure. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Делегирование BLOB-объектов хранилища](#storage-blob-delegator) | Получите ключ делегирования пользователя, который затем можно использовать для создания подписи общего доступа для контейнера или большого двоичного объекта, подписанного с помощью учетных данных Azure AD. Дополнительные сведения см. [в разделе Создание SAS для делегирования пользователей](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Участник общей папки SMB данных файлов хранилища](#storage-file-data-smb-share-contributor) | Разрешает доступ на чтение, запись и удаление для файлов и каталогов в общих файловых ресурсах Azure. Эта роль не имеет встроенных эквивалентов на файловых серверах Windows. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Участник хранилища данных файлового ресурса SMB с повышенными правами](#storage-file-data-smb-share-elevated-contributor) | Разрешение на чтение, запись, удаление и изменение списков управления доступом для файлов и каталогов в общих папках Azure. Эта роль эквивалентна общей папке ACL для изменения файловых серверов Windows. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Средство чтения общего ресурса SMB с данными файлов хранилища](#storage-file-data-smb-share-reader) | Разрешает доступ на чтение к файлам и каталогам в общих файловых ресурсах Azure. Эта роль эквивалентна общей папке ACL для чтения на файловых серверах Windows. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Участник данных очереди хранилища](#storage-queue-data-contributor) | Чтение, запись и удаление очередей службы хранилища Azure и сообщений очереди. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Обработчик сообщений данных очереди хранилища](#storage-queue-data-message-processor) | Просмотр, извлечение и удаление сообщения из очереди службы хранилища Azure. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Отправитель сообщений очереди хранилища](#storage-queue-data-message-sender) | Добавление сообщений в очередь службы хранилища Azure. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Модуль чтения данных очереди хранилища](#storage-queue-data-reader) | Чтение и вывод списка очередей службы хранилища Azure и сообщений очереди. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Web** |  |  |
> | [Модуль чтения данных Azure Maps](#azure-maps-data-reader) | Предоставляет доступ для чтения данных, связанных с картой, из учетной записи Azure Maps. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Участник службы поиска](#search-service-contributor) | Позволяет управлять службами поиска, но не доступом к ним. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Участник веб-плана](#web-plan-contributor) | Позволяет управлять веб-планами для веб-сайтов, но не доступом к ним. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Участник веб-сайта](#website-contributor) | Позволяет управлять веб-сайтами (не веб-планами), но не доступом к ним. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Контейнеры** |  |  |
> | [акрделете](#acrdelete) | Удаление записи контроля доступа | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | Средство подписывания образов ACR. | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | Извлечение ACR. | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | Отправка ACR. | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | Модуль чтения данных карантина ACR. | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | Модуль записи данных карантина ACR. | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Роль администратора кластера в Службе Azure Kubernetes](#azure-kubernetes-service-cluster-admin-role) | Список действий, выполненных с помощью учетных данных администратора кластера. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Роль пользователя кластера в Службе Azure Kubernetes](#azure-kubernetes-service-cluster-user-role) | Список действий, выполненных с помощью учетных данных пользователя кластера. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Базы данных** |  |  |
> | [Роль читателя учетных записей Cosmos DB](#cosmos-db-account-reader-role) | Позволяет считывать данные учетных записей Azure Cosmos DB. Сведения об управлении учетными записями Azure Cosmos DB см. в разделе [Участник учетной записи DocumentDB](#documentdb-account-contributor). | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Оператор Cosmos DB](#cosmos-db-operator) | Позволяет управлять учетными записями Azure Cosmos DB, но не доступом к данным. Предотвращает доступ к ключам учетной записи и строкам подключения. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Позволяет отправлять запрос на восстановление контейнера или базы данных Cosmos DB для учетной записи. | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [Участник учетной записи DocumentDB](#documentdb-account-contributor) | Может управлять учетными записями Azure Cosmos DB Служба Azure Cosmos DB раньше называлась DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Участник кэша Redis](#redis-cache-contributor) | Позволяет управлять кэшем Redis, но не доступом к нему. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [Участник базы данных SQL](#sql-db-contributor) | Позволяет управлять базами данных SQL, но не доступом к ним. Кроме того, не позволяет управлять их политиками безопасности или родительскими серверами SQL Server. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [Участник SQL Управляемый экземпляр](#sql-managed-instance-contributor) | Позволяет управлять управляемыми экземплярами SQL и требуемой конфигурацией сети, но не может предоставлять доступ другим пользователям. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [Диспетчер безопасности SQL](#sql-security-manager) | Позволяет управлять политиками безопасности серверов SQL Server и баз данных SQL, но не доступом к ним. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [Участник SQL Server](#sql-server-contributor) | Позволяет управлять серверами и базами данных SQL, но не доступом к ним, а не с их политиками безопасности. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Аналитика** |  |  |
> | [Владелец данных концентраторов событий Azure](#azure-event-hubs-data-owner) | Обеспечивает полный доступ к ресурсам концентраторов событий Azure. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Приемник данных концентраторов событий Azure](#azure-event-hubs-data-receiver) | Разрешает получать доступ к ресурсам концентраторов событий Azure. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Отправитель данных концентраторов событий Azure](#azure-event-hubs-data-sender) | Разрешает отправку доступа к ресурсам концентраторов событий Azure. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Участник фабрики данных](#data-factory-contributor) | Создание фабрик данных и управление ими, а также их дочерними ресурсами. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Средство очистки данных](#data-purger) | Может выполнить очистку данных аналитики. | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [Оператор кластера HDInsight](#hdinsight-cluster-operator) | Позволяет читать и изменять конфигурации кластера HDInsight. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [Участник доменных служб HDInsight](#hdinsight-domain-services-contributor) | Позволяет читать, создавать, изменять и удалять операции, связанные с доменными службами, необходимыми для Корпоративного пакета безопасности HDInsight | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Участник Log Analytics](#log-analytics-contributor) | Участник Log Analytics может считывать все данные мониторинга и изменять параметры мониторинга. Изменение параметров мониторинга подразумевает добавление расширений в виртуальные машины, чтение ключей учетной записи хранения для настройки коллекции журналов в службе хранилища Azure, создание и настройку учетных записей службы автоматизации, добавление решений и настройку диагностики Azure во всех ресурсах Azure. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [читатель Log Analytics;](#log-analytics-reader) | Читатель Log Analytics может просматривать все данные мониторинга, выполнять по ним поиск и просматривать параметры мониторинга, в том числе конфигурацию системы диагностики Azure для всех ресурсов Azure. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Блокчейн** |  |  |
> | [Доступ к узлу-члену блокчейн (Предварительная версия)](#blockchain-member-node-access-preview) | Разрешает доступ к узлам члена Блокчейн | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **Искусственный интеллект и машинное обучение** |  |  |
> | [Участник служб Cognitive Services](#cognitive-services-contributor) | Позволяет создавать, читать, обновлять, удалять ключи служб Cognitive Services и управлять ими. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Cognitive Services модуль чтения данных (Предварительная версия)](#cognitive-services-data-reader-preview) | Позволяет считывать данные Cognitive Services. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Пользователь служб Cognitive Services](#cognitive-services-user) | Позволяет создавать и читать список ключей служб Cognitive Services. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Смешанная реальность** |  |  |
> | [Участник учетной записи пространственных привязок](#spatial-anchors-account-contributor) | Позволяет управлять пространственными привязками в учетной записи, но не удалять их | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Владелец учетной записи пространственных привязок](#spatial-anchors-account-owner) | Позволяет управлять пространственными привязками в учетной записи, включая их удаление. | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Читатель учетной записи пространственных привязок](#spatial-anchors-account-reader) | Позволяет нахождение и чтение свойств пространственных привязок в учетной записи. | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Интеграция** |  |  |
> | [Участник службы управления API](#api-management-service-contributor) | Может управлять службой и интерфейсами API. | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [Роль оператора службы управления API](#api-management-service-operator-role) | Может управлять службой, но не интерфейсами API. | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [Роль читателя данных службы управления API](#api-management-service-reader-role) | Доступ к службе и интерфейсам API в режиме "только для чтения". | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Владелец данных конфигурации приложения](#app-configuration-data-owner) | Разрешает полный доступ к данным конфигурации приложения. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Модуль чтения данных конфигурации приложений](#app-configuration-data-reader) | Разрешает доступ на чтение к данным конфигурации приложения. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Владелец данных служебной шины Azure](#azure-service-bus-data-owner) | Обеспечивает полный доступ к ресурсам служебной шины Azure. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Приемник данных служебной шины Azure](#azure-service-bus-data-receiver) | Разрешает получать доступ к ресурсам служебной шины Azure. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Отправитель данных служебной шины Azure](#azure-service-bus-data-sender) | Разрешает отправку доступа к ресурсам служебной шины Azure. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Владелец регистрации Azure Stack](#azure-stack-registration-owner) | Позволяет управлять регистрациями Azure Stack. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [Участник EventGrid EventSubscription](#eventgrid-eventsubscription-contributor) | Позволяет управлять операциями с подписками на события Сетки событий. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [Читатель EventGrid EventSubscription](#eventgrid-eventsubscription-reader) | Позволяет получить доступ на чтение к подпискам на события Сетки событий. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [Участник учетной записи интеллектуальных систем](#intelligent-systems-account-contributor) | Позволяет управлять учетными записями интеллектуальных систем, но не доступом к ним. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Создатель приложений логики](#logic-app-contributor) | Позволяет управлять приложениями логики, но не изменяет доступ к ним. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Оператор приложений логики](#logic-app-operator) | Позволяет читать, включать и отключать приложения логики, но не изменять и не обновлять их. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Удостоверение** |  |  |
> | [Участник управляемого удостоверения](#managed-identity-contributor) | Создание, чтение, обновление и удаление пользовательских удостоверений. | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Оператор управляемого удостоверения](#managed-identity-operator) | Чтение и назначение пользовательских удостоверений. | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Безопасность** |  |  |
> | [Участник Sentinel Azure](#azure-sentinel-contributor) | Участник Sentinel Azure | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Читатель маркеров Azure](#azure-sentinel-reader) | Читатель маркеров Azure | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Ответчик Sentinel Azure](#azure-sentinel-responder) | Ответчик Sentinel Azure | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Участник Key Vault](#key-vault-contributor) | Позволяет управлять хранилищами ключей, но не доступом к ним. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Администратор безопасности](#security-admin) | Просмотр и обновление разрешений для центра безопасности. Те же разрешения, что и у роли читателя безопасности, также могут обновлять политику безопасности и закрывать предупреждения и рекомендации. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Участник оценки безопасности](#security-assessment-contributor) | Позволяет отправлять оценки в центр безопасности | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Диспетчер безопасности (устаревший)](#security-manager-legacy) | Это устаревшая роль. Используйте вместо него администратора безопасности. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Читатель безопасности](#security-reader) | Просмотр разрешений для центра безопасности. Может просматривать рекомендации, предупреждения, политику безопасности и состояния безопасности, но не может вносить изменения. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [Пользователь DevTest Labs](#devtest-labs-user) | Позволяет подключать, запускать, перезапускать виртуальные машины и завершать их работу в Azure DevTest Labs. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Создатель лаборатории](#lab-creator) | Позволяет создавать, администрировать и удалять управляемые лаборатории в учетных записях Служб лабораторий Azure. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Монитор** |  |  |
> | [Участник компонента Application Insights](#application-insights-component-contributor) | Может управлять компонентами Application Insights | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Отладчик моментальных снимков Application Insights](#application-insights-snapshot-debugger) | Пользователю предоставляется разрешение на просмотр и загрузку моментальных снимков отладки, собранных с помощью Application Insights Snapshot Debugger. Обратите внимание, что эти разрешения не включены в роли [Владелец](#owner) или [Участник](#contributor). | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Monitoring Contributor](#monitoring-contributor) | Может читать все данные мониторинга и изменять параметры мониторинга. Ознакомьтесь также со статьей [Приступая к работе с ролями, разрешениями и системой безопасности с помощью Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Издатель метрик мониторинга](#monitoring-metrics-publisher) | Включает публикацию метрик ресурсов Azure | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Monitoring Reader](#monitoring-reader) (Читатель данных мониторинга) | Может читать все данные мониторинга (метрики, журналы и т. д.). Ознакомьтесь также со статьей [Приступая к работе с ролями, разрешениями и системой безопасности с помощью Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Участник книги](#workbook-contributor) | Может сохранять общие книги. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Читатель книг](#workbook-reader) | Может считывать книги. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Управление + руководство** |  |  |
> | [Оператор заданий службы автоматизации](#automation-job-operator) | Создание заданий и управление ими с помощью модулей Runbook службы автоматизации. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Оператор службы автоматизации](#automation-operator) | Операторы автоматизации могут запускать, останавливать, приостанавливать и возобновлять задания. | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Оператор Runbook службы автоматизации](#automation-runbook-operator) | Чтение свойств Runbook, позволяющее создавать задания Runbook. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Подключение к подключенному компьютеру Azure](#azure-connected-machine-onboarding) | Может подключить подключенные к Azure компьютеры. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Администратор ресурсов подключенного компьютера Azure](#azure-connected-machine-resource-administrator) | Может читать, записывать, удалять и повторно использовать подключенные к Azure компьютеры. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Читатель выставления счетов](#billing-reader) | Разрешает читать данные выставления счетов. | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Участник схемы](#blueprint-contributor) | Позволяет управлять определениями схем, но не назначать их. | 41077137-E803-4205-871c-5a86e6a753b4 |
> | [Оператор схемы](#blueprint-operator) | Может назначать существующие опубликованные проекты, но не может создавать новые. Обратите внимание, что это работает только в том случае, если назначение выполняется с управляемым удостоверением, назначенным пользователем. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Участник управления затратами](#cost-management-contributor) | Позволяет просматривать расходы и управлять конфигурацией затрат (например, бюджеты, экспорты) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Читатель службы "Управление затратами"](#cost-management-reader) | Позволяет просматривать данные о расходах и конфигурации (например, бюджеты, экспорты) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Роль участника управляемого приложения](#managed-application-contributor-role) | Позволяет создавать ресурсы управляемых приложений. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Роль оператора управляемого приложения](#managed-application-operator-role) | Позволяет читать и выполнять действия с ресурсами управляемого приложения. | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Читатель Управляемых приложений](#managed-applications-reader) | Позволяет считывать ресурсы в управляемом приложении и запрашивать доступ JIT. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Удаление роли назначения регистрации управляемых служб](#managed-services-registration-assignment-delete-role) | Роль удаления назначения регистрации управляемых служб позволяет управлять пользователями клиента для удаления назначения регистрации, назначенного их клиенту. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Участник группы управления](#management-group-contributor) | Роль участника группы управления | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Читатель группы управления](#management-group-reader) | Роль читателя группы управления | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Участник учетной записи New Relic APM](#new-relic-apm-account-contributor) | Позволяет управлять учетными записями и приложениями New Relic Application Performance Management, но не доступом к ним. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Средство записи данных Policy Insights (Предварительная версия)](#policy-insights-data-writer-preview) | Разрешает доступ на чтение к политикам ресурсов и доступ на запись к событиям политики компонента ресурсов. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Участник политики ресурсов](#resource-policy-contributor) | Пользователи с правами на создание и изменение политики ресурсов, создание запроса в службу поддержки и чтение ресурсов и иерархии. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Участник Site Recovery](#site-recovery-contributor) | Позволяет управлять службой Site Recovery, за исключением создания хранилищ и назначения ролей. | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Оператор Site Recovery](#site-recovery-operator) | Позволяет выполнять отработку отказа и восстановление размещения, но не другие операции управления Site Recovery. | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Читатель Site Recovery](#site-recovery-reader) | Позволяет просматривать состояние Site Recovery без выполнения других операций управления. | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Support Request Contributor (Участник с правом создавать запросы на поддержку)](#support-request-contributor) | Позволяет создавать запросы в службу поддержки и управлять ими. | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Участник тега](#tag-contributor) | Позволяет управлять тегами в сущностях, не предоставляя доступ к самим сущностям. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Прочее** |  |  |
> | [Участник BizTalk](#biztalk-contributor) | Позволяет управлять службами BizTalk, но не доступом к ним. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Участник коллекции заданий планировщика](#scheduler-job-collections-contributor) | Позволяет управлять коллекциями заданий планировщика, но не доступом к ним. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |


## <a name="general"></a>Общие сведения


### <a name="contributor"></a>Участник

Позволяет управлять всем, кроме предоставления доступа к ресурсам.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | * | Создание ресурсов всех типов и управление ими |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | Удаление ролей, назначений политик, определений политик и определений наборов политик |
> | Microsoft.Authorization/*/Write | Создание ролей, назначений ролей, назначений политик, определений политик и определений наборов политик |
> | Microsoft.Authorization/elevateAccess/Action | Предоставляет вызывающему доступ с правами администратора для области действия клиента. |
> | Microsoft.Blueprint/blueprintAssignments/write | Создание или обновление назначений в схеме |
> | Microsoft.Blueprint/blueprintAssignments/delete | Удаление любых назначений в схеме |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>Владелец

Позволяет управлять всем, в том числе доступом к ресурсам.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | * | Создание ресурсов всех типов и управление ими |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything, including access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>Читатель

Может просматривать все элементы, но не вносить изменения.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything, but not make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>Администратор доступа пользователей

Позволяет управлять доступом пользователей к ресурсам Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | Microsoft.Authorization/* | Управление авторизацией |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>Службы вычислений


### <a name="classic-virtual-machine-contributor"></a>Участник классической виртуальной машины

Позволяет управлять классическими виртуальными машинами, но не доступом к ним и не учетной записью виртуальной сети или хранения, к которой они подключены.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.ClassicCompute/domainNames/* | Создание доменных имен для классических вычислений и управление ими |
> | Microsoft.ClassicCompute/virtualMachines/* | Создание виртуальных машин и управление ими |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Привязывает зарезервированный IP-адрес. |
> | Microsoft.ClassicNetwork/reservedIps/read | Возвращает зарезервированные IP-адреса. |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Подключает виртуальную сеть. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Возвращает виртуальную сеть. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | Возвращает диск в учетной записи хранения. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | Возвращает образ в учетной записи хранения. (Не рекомендуется к использованию. Следует использовать Microsoft.ClassicStorage/storageAccounts/vmImages.) |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Отображает ключи доступа для учетных записей хранения. |
> | Microsoft.ClassicStorage/storageAccounts/read | Возвращает учетную запись хранения для заданной учетной записи. |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>Администратор виртуальной машины

Позволяет просматривать виртуальные машины на портале и входить в систему с правами администратора.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Network/publicIPAddresses/read | Возвращает определение общедоступного IP-адреса. |
> | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Microsoft.Network/loadBalancers/read | Возвращает определение подсистемы балансировки нагрузки. |
> | Microsoft.Network/networkInterfaces/read | Возвращает определение сетевого интерфейса.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft.Compute/virtualMachines/login/action | Вход в систему на виртуальной машине в качестве обычного пользователя. |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Вход в систему на виртуальной машине с правами администратора Windows или привилегированного пользователя Linux. |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>Участник виртуальной машины

Позволяет управлять виртуальными машинами, но не доступом к ним и не виртуальной сетью или учетной записью хранения, к которой они подключены.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Compute/availabilitySets/* | Создание групп доступности вычислений и управление ими |
> | Microsoft.Compute/locations/* | Создание расположений вычислений и управление ими |
> | Microsoft.Compute/virtualMachines/* | Создание виртуальных машин и управление ими |
> | Microsoft.Compute/virtualMachineScaleSets/* | Создание наборов масштабирования виртуальных машин и управление ими |
> | Microsoft.Compute/disks/write | Создает новый диск или обновляет существующий. |
> | Microsoft.Compute/disks/read | Возвращает свойства диска. |
> | Microsoft.Compute/disks/delete | Удаляет диск. |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Присоединяет внутренний пул адресов шлюза приложений. Не оповещать. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Присоединяет внутренний пул адресов подсистемы балансировки нагрузки. Не оповещать. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Присоединяется к пулу входящих подключений NAT подсистемы балансировки нагрузки. Не оповещать. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Присоединяет правило NAT для входящего трафика подсистемы балансировки нагрузки. Не оповещать. |
> | Microsoft.Network/loadBalancers/probes/join/action | Разрешение использования зондов подсистемы балансировки нагрузки. Например, при наличии этого разрешения свойство healthProbe в масштабируемом наборе виртуальных машин может ссылаться на конкретный зонд. Не оповещать. |
> | Microsoft.Network/loadBalancers/read | Возвращает определение подсистемы балансировки нагрузки. |
> | Microsoft.Network/locations/* | Создание сетевых расположений и управление ими |
> | Microsoft.Network/networkInterfaces/* | Создание сетевых интерфейсов и управление ими |
> | Microsoft.Network/networkSecurityGroups/join/action | Присоединяет группу безопасности сети. Не оповещать. |
> | Microsoft.Network/networkSecurityGroups/read | Возвращает определение группы безопасности сети. |
> | Microsoft.Network/publicIPAddresses/join/action | Присоединяет общедоступный IP-адрес. Не оповещать. |
> | Microsoft.Network/publicIPAddresses/read | Возвращает определение общедоступного IP-адреса. |
> | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Присоединяет виртуальную сеть. Не оповещать. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Создание цели защиты для резервной копии. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Возвращает сведения об объекте для защищенного элемента. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Создает элемент, защищенный службой архивации. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Возвращает все политики защиты. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Создает политику защиты. |
> | Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/usages/read | Возвращает данные об использовании хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/write | Создает операцию создания хранилища, которая создает ресурс Azure типа "хранилище". |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
> | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>Пользователь виртуальной машины

Позволяет просматривать виртуальные машины на портале и входить в систему с правами обычного пользователя.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Network/publicIPAddresses/read | Возвращает определение общедоступного IP-адреса. |
> | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Microsoft.Network/loadBalancers/read | Возвращает определение подсистемы балансировки нагрузки. |
> | Microsoft.Network/networkInterfaces/read | Возвращает определение сетевого интерфейса.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft.Compute/virtualMachines/login/action | Вход в систему на виртуальной машине в качестве обычного пользователя. |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>Сеть


### <a name="cdn-endpoint-contributor"></a>Участник конечных точек CDN

Может управлять конечными точками CDN, но не может предоставлять доступ другим пользователям.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>Читатель конечной точки CDN

Может просматривать конечные точки CDN, но не может вносить изменения.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>Участник профиля CDN

Может управлять профилями CDN и их конечными точками, но не может предоставлять доступ другим пользователям.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>Читатель данных профиля CDN

Может просматривать профили CDN и их конечные точки, но не может вносить изменения.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>Участник классической сети

Позволяет управлять классическими сетями, но не доступом к ним.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.ClassicNetwork/* | Создание классических сетей и управление ими |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>Участник зоны DNS

Позволяет управлять зонами DNS и наборами записей в Azure DNS, но не тем, кому они будут доступны.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Network/dnsZones/* | Создание зон и записей DNS и управление ими. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>Участник сети

Позволяет управлять сетями, но не доступом к ним.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Network/* | Создание сетей и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Участник диспетчера трафика

Позволяет управлять профилями диспетчера трафика, но не доступом к ним.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>Память


### <a name="avere-contributor"></a>Участник авере

Может создавать кластер Авере Вфкст и управлять им.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft. COMPUTE/*/Реад |  |
> | Microsoft.Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft. COMPUTE/Disks/* |  |
> | Microsoft. Network/*/Реад |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Microsoft.Network/virtualNetworks/subnets/read | Возвращает определение подсети виртуальной сети. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Присоединяет виртуальную сеть. Не оповещать. |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Присоединение к подсети ресурса, например учетной записи хранения или базы данных SQL. Не оповещать. |
> | Microsoft.Network/networkSecurityGroups/join/action | Присоединяет группу безопасности сети. Не оповещать. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft. Storage/*/Реад |  |
> | Microsoft.Storage/storageAccounts/* | Создание учетных записей хранения и управление ими |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | Microsoft. Resources/Subscriptions/resourceGroups/Resources/Read | Возвращает ресурсы группы ресурсов. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Возвращение результата, полученного при удалении большого двоичного объекта. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Возвращение большого двоичного объекта или списка таких объектов. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Возвращение результата, полученного при записи большого двоичного объекта. |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>Оператор авере

Используется кластером Авере Вфкст для управления кластером.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Compute/virtualMachines/read | Возвращает свойства виртуальной машины. |
> | Microsoft.Network/networkInterfaces/read | Возвращает определение сетевого интерфейса.  |
> | Microsoft.Network/networkInterfaces/write | Создает новый сетевой интерфейс или обновляет существующий.  |
> | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Microsoft.Network/virtualNetworks/subnets/read | Возвращает определение подсети виртуальной сети. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Присоединяет виртуальную сеть. Не оповещать. |
> | Microsoft.Network/networkSecurityGroups/join/action | Присоединяет группу безопасности сети. Не оповещать. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Возвращение результата удаления контейнера. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Возвращает список контейнеров. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Возвращает результат размещения контейнера больших двоичных объектов. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Возвращение результата, полученного при удалении большого двоичного объекта. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Возвращение большого двоичного объекта или списка таких объектов. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Возвращение результата, полученного при записи большого двоичного объекта. |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>Участник резервного копирования

Позволяет управлять службой архивации, но не может создавать хранилища и предоставлять доступ другим пользователям.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Управление результатами операций управления резервным копированием |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Создание контейнеров резервных копий внутри структуры резервного копирования хранилища служб восстановления и управление этими контейнерами |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Обновляет список контейнеров. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Создание заданий резервного копирования и управление ими |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Экспортирует задания. |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Создание результатов операций управления резервным копированием и управление ими |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Создание политик резервного копирования и управление ими |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Создание элементов, для которых можно создавать резервные копии, и управление ими |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Создание элементов, включаемых в резервную копию, и управление ими |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Создание контейнеров с элементами, включаемыми в резервную копию, и управление такими контейнерами |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Возвращает сводки по защищенным элементам и защищенным серверам для служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Создание сертификатов, связанных с резервной копией в хранилище служб восстановления, и управление ими |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Создание расширенных сведений, связанных с хранилищем, и управление ими |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Возвращает оповещения для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Создание зарегистрированных удостоверений и управление ими |
> | Microsoft.RecoveryServices/Vaults/usages/* | Создание хранилища служб восстановления и управление его использованием |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Проверка операций для защищенного элемента |
> | Microsoft.RecoveryServices/Vaults/write | Создает операцию создания хранилища, которая создает ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Возвращает состояние операции архивации для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Возвращение всех серверов управления архивацией, зарегистрированных в хранилище. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Получение всех защищаемых контейнеров. |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Проверка состояния архивации хранилищ служб восстановления. |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Проверка компонентов. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Разрешение оповещения. |
> | Microsoft.RecoveryServices/operations/read | Получение списка операций для поставщика ресурсов. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Возвращает состояние операции для данной операции |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Вывод списка всех целей защиты для резервного копирования. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>Оператор резервного копирования

Позволяет управлять службами архивации, но не удалять архивные копии, создавать хранилища или предоставлять доступ другим пользователям

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Возвращает состояние операции. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Возвращает результат операции, выполненной с контейнером защиты. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Архивирует защищенный элемент. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Возвращает результат операции, выполненной с защищенными элементами. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Возвращает состояние операции, выполненной с защищенными элементами. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Возвращает сведения об объекте для защищенного элемента. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Подготавливает мгновенное восстановление для защищенного элемента. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Возвращает точки восстановления для защищенных элементов. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Восстанавливает точки восстановления для защищенных элементов. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Отменяет мгновенное восстановление для защищенного элемента. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Создает элемент, защищенный службой архивации. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Возвращает все зарегистрированные контейнеры. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Обновляет список контейнеров. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Создание заданий резервного копирования и управление ими |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Экспортирует задания. |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Создание результатов операций управления резервным копированием и управление ими |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Возвращает результаты операции политики. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Возвращает все политики защиты. |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Создание элементов, для которых можно создавать резервные копии, и управление ими |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Возвращает список всех защищенных элементов. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Возвращает все контейнеры, принадлежащие подписке. |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Возвращает сводки по защищенным элементам и защищенным серверам для служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Операция обновления сертификата ресурса обновляет сертификат учетных данных для ресурса или хранилища. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Возвращает оповещения для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Операцию получения результатов операции можно использовать, чтобы получить состояние и результат асинхронно отправленной операции. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Операцию получения контейнеров можно использовать для получения контейнеров, зарегистрированных для ресурса. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Операцию регистрации контейнера в службе можно использовать для регистрации контейнера в службе восстановления. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Возвращает данные об использовании хранилища служб восстановления. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Проверка операций для защищенного элемента |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Возвращает состояние операции архивации для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Возвращает состояние операции политики. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Создание зарегистрированного контейнера. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Запрос рабочих нагрузок в контейнере. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Возвращение всех серверов управления архивацией, зарегистрированных в хранилище. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Создание цели защиты для резервной копии. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Получение цели защиты для резервного копирования. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Получение всех защищаемых контейнеров. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Получение всех элементов в контейнере. |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Проверка состояния архивации хранилищ служб восстановления. |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Проверка компонентов. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Разрешение оповещения. |
> | Microsoft.RecoveryServices/operations/read | Получение списка операций для поставщика ресурсов. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Возвращает состояние операции для данной операции |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Вывод списка всех целей защиты для резервного копирования. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>Читатель резервных копий

Может просматривать службы резервного копирования, но не может вносить изменения.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp является внутренней операцией, используемой службой. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Возвращает состояние операции. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Возвращает результат операции, выполненной с контейнером защиты. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Возвращает результат операции, выполненной с защищенными элементами. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Возвращает состояние операции, выполненной с защищенными элементами. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Возвращает сведения об объекте для защищенного элемента. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Возвращает точки восстановления для защищенных элементов. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Возвращает все зарегистрированные контейнеры. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Возвращает результат операции задания. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Возвращает все объекты заданий. |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Экспортирует задания. |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Возвращает результат операции архивации для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Возвращает результаты операции политики. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Возвращает все политики защиты. |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Возвращает список всех защищенных элементов. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Возвращает все контейнеры, принадлежащие подписке. |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Возвращает сводки по защищенным элементам и защищенным серверам для служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Возвращает оповещения для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Операцию получения результатов операции можно использовать, чтобы получить состояние и результат асинхронно отправленной операции. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Операцию получения контейнеров можно использовать для получения контейнеров, зарегистрированных для ресурса. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Возвращает конфигурацию службы хранилища для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Возвращает конфигурацию хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Возвращает состояние операции архивации для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Возвращает состояние операции политики. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Возвращение всех серверов управления архивацией, зарегистрированных в хранилище. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Получение цели защиты для резервного копирования. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Получение всех элементов в контейнере. |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Проверка состояния архивации хранилищ служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Разрешение оповещения. |
> | Microsoft.RecoveryServices/operations/read | Получение списка операций для поставщика ресурсов. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Возвращает состояние операции для данной операции |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Вывод списка всех целей защиты для резервного копирования. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Возвращает данные об использовании хранилища служб восстановления. |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Проверка компонентов. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>Участник классической учетной записи хранения

Позволяет управлять классическими учетными записями хранения, но не предоставлять доступ к ним.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.ClassicStorage/storageAccounts/* | Создание учетных записей хранения и управление ими |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>Роль службы оператора ключей классических учетных записей хранения

Операторы ключей классических учетных записей хранения могут перечислять и повторно создавать ключи в классических учетных записях хранения.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Отображает ключи доступа для учетных записей хранения. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Повторно создает существующие ключи доступа для учетной записи хранения. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>Участник Data Box

Позволяет управлять всеми данными службы Data Box, кроме предоставления доступа другим пользователям.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | Microsoft.Databox/* |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>Читатель Data Box

Позволяет управлять службой Data Box, но не позволяет создавать заказы и менять их порядок, а также предоставлять доступ другим пользователям.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Databox/*/read |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | Выводит список незашифрованных учетных данных, связанных с заказом. |
> | Microsoft.Databox/locations/availableSkus/action | Этот метод возвращает список доступных номеров SKU. |
> | Microsoft. Датабокс/Locations/Валидатеинпутс/Action | Этот метод выполняет все типы проверок. |
> | Microsoft. Датабокс/Locations/Регионконфигуратион/Action | Этот метод возвращает конфигурации для региона. |
> | Microsoft. Датабокс/Locations/Валидатеаддресс/Action | Проверяет адрес доставки и предлагает альтернативные адреса, если они доступны. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Разработчик Data Lake Analytics

Позволяет отправлять, отслеживать задания и управлять ими, но не позволяет создавать или удалять учетные записи Data Lake Analytics.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | Удаление учетной записи Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Предоставление разрешений на отмену заданий, отправленных другими пользователями. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Создание или обновление учетной записи Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Создание или обновление связанной учетной записи Data Lake Store для учетной записи Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Отмена связи учетной записи Data Lake Store с учетной записью Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Создание или обновление связанной учетной записи хранения для учетной записи Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Удаление связи учетной записи хранения с учетной записью Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Создает или обновляет правило брандмауэра. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Удаляет правило брандмауэра. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Создание или обновление политики вычислений. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Удаление политики вычислений. |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>Модуль чтения и доступ к данным

Позволяет просматривать все данные, но не позволит удалить или создать учетную запись хранения или содержащий ресурс. Он также предоставит доступ на чтение и запись для всех данных, содержащихся в учетной записи хранения через доступ к ключам учетной записи хранения.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
> | Microsoft. Storage/storageAccounts/Листаккаунтсас/действие | Возвращает маркер SAS для указанной учетной записи хранения. |
> | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>Участник учетной записи хранения

Разрешает управление учетными записями хранения. Предоставляет доступ к ключу учетной записи, который можно использовать для доступа к данным через авторизацию с общим ключом.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Insights/diagnosticSettings/* | Создание, обновление или считывание параметра диагностики для сервера анализа данных. |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Присоединение к подсети ресурса, например учетной записи хранения или базы данных SQL. Не оповещать. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Storage/storageAccounts/* | Создание учетных записей хранения и управление ими |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>Роль службы оператора ключей учетных записей хранения

Разрешает перечисление и повторное создание ключей доступа к учетной записи хранения.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Повторно создает ключи доступа для указанной учетной записи хранения. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>участник данных BLOB-объектов хранилища;

Чтение, запись и удаление контейнеров службы хранилища Azure и больших двоичных объектов. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Удаление контейнера. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Возврат контейнера или списка контейнеров. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Измените метаданные или свойства контейнера. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Возвращает ключ делегирования пользователя для службы BLOB-объектов. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Удалить большой двоичный объект. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Возврат большого двоичного объекта или списка больших двоичных объектов. |
> | Microsoft. Storage/storageAccounts/Блобсервицес/контейнеры/BLOB-объекты/перемещение/действие | Перемещает большой двоичный объект из одного пути в другой |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Запись в большой двоичный объект. |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>владелец данных BLOB-объектов хранилища;

Предоставляет полный доступ к контейнерам и данным больших двоичных объектов службы хранилища Azure, включая назначение управления доступом POSIX. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | Полные разрешения на контейнеры. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Возвращает ключ делегирования пользователя для службы BLOB-объектов. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | Полные разрешения на большие двоичные объекты. |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>читатель данных больших двоичных объектов хранилища.

Прочтите и перечислите контейнеры и большие двоичные объекты службы хранилища Azure. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Возврат контейнера или списка контейнеров. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Возвращает ключ делегирования пользователя для службы BLOB-объектов. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Возврат большого двоичного объекта или списка больших двоичных объектов. |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>Делегирование BLOB-объектов хранилища

Получите ключ делегирования пользователя, который затем можно использовать для создания подписи общего доступа для контейнера или большого двоичного объекта, подписанного с помощью учетных данных Azure AD. Дополнительные сведения см. [в разделе Создание SAS для делегирования пользователей](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Возвращает ключ делегирования пользователя для службы BLOB-объектов. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>Участник общей папки SMB данных файлов хранилища

Разрешает доступ на чтение, запись и удаление для файлов и каталогов в общих файловых ресурсах Azure. Эта роль не имеет встроенных эквивалентов на файловых серверах Windows.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | *отсутствуют* |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft. Storage/storageAccounts/Филесервицес/общие папки/файлы/чтение | Возвращает файл или папку или список файлов и папок. |
> | Microsoft. Storage/storageAccounts/Филесервицес/общие папки/файлы/запись | Возвращает результат записи файла или создания папки. |
> | Microsoft. Storage/storageAccounts/Филесервицес/общие папки/Files/Delete | Возвращает результат удаления файла или папки. |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Участник хранилища данных файлового ресурса SMB с повышенными правами

Разрешение на чтение, запись, удаление и изменение списков управления доступом для файлов и каталогов в общих папках Azure. Эта роль эквивалентна общей папке ACL для изменения файловых серверов Windows.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | *отсутствуют* |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft. Storage/storageAccounts/Филесервицес/общие папки/файлы/чтение | Возвращает файл или папку или список файлов и папок. |
> | Microsoft. Storage/storageAccounts/Филесервицес/общие папки/файлы/запись | Возвращает результат записи файла или создания папки. |
> | Microsoft. Storage/storageAccounts/Филесервицес/общие папки/Files/Delete | Возвращает результат удаления файла или папки. |
> | Microsoft. Storage/storageAccounts/Филесервицес/общие папки/Files/модифипермиссионс/Action | Возвращает результат изменения разрешения для файла или папки. |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>Средство чтения общего ресурса SMB с данными файлов хранилища

Разрешает доступ на чтение к файлам и каталогам в общих файловых ресурсах Azure. Эта роль эквивалентна общей папке ACL для чтения на файловых серверах Windows.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | *отсутствуют* |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft. Storage/storageAccounts/Филесервицес/общие папки/файлы/чтение | Возвращает файл или папку или список файлов и папок. |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>Участник данных очереди хранилища

Чтение, запись и удаление очередей службы хранилища Azure и сообщений очереди. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Удаление очереди. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Возврат очереди или списка очередей. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Измените метаданные или свойства очереди. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Удаление одного или нескольких сообщений из очереди. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Просмотр или извлечение одного или нескольких сообщений из очереди. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Добавление сообщения в очередь. |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>Обработчик сообщений данных очереди хранилища

Просмотр, извлечение и удаление сообщения из очереди службы хранилища Azure. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | *отсутствуют* |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Просмотр сообщения. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Получение и удаление сообщения. |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>Отправитель сообщений очереди хранилища

Добавление сообщений в очередь службы хранилища Azure. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | *отсутствуют* |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Добавление сообщения в очередь. |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>Модуль чтения данных очереди хранилища

Чтение и вывод списка очередей службы хранилища Azure и сообщений очереди. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Возвращение очереди или списка очередей. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Просмотр или извлечение одного или нескольких сообщений из очереди. |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>Интернет


### <a name="azure-maps-data-reader"></a>Модуль чтения данных Azure Maps

Предоставляет доступ для чтения данных, связанных с картой, из учетной записи Azure Maps.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | *отсутствуют* |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft. Maps/Accounts/*/Реад |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Участник службы поиска

Позволяет управлять службами поиска, но не доступом к ним.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Search/searchServices/* | Создание служб поиска и управление ими |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Участник веб-плана

Позволяет управлять веб-планами для веб-сайтов, но не доступом к ним.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | Microsoft.Web/serverFarms/* | Создание ферм серверов и управление ими |
> | Microsoft. Web/hostingEnvironments/Join/действие | Присоединяет Среда службы приложений |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>Участник веб-сайта

Позволяет управлять веб-сайтами (не веб-планами), но не доступом к ним.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Insights/components/* | Создание компонентов Insights и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | Microsoft.Web/certificates/* | Создание сертификатов веб-сайтов и управление ими |
> | Microsoft.Web/listSitesAssignedToHostName/read | Возвращает имена сайтов, назначенные имени узла. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Возвращает свойства плана службы приложений. |
> | Microsoft.Web/sites/* | Создание веб-сайтов и управление ими (создание сайта также требует разрешений на запись к связанному плану службы приложений). |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>Контейнеры


### <a name="acrdelete"></a>акрделете

Удаление записи контроля доступа

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft. ContainerRegistry/реестры/артефакты/удаление | Удаление артефакта в реестре контейнеров. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>AcrImageSigner

Средство подписывания образов ACR.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.ContainerRegistry/registries/sign/write | Передача или извлечение метаданных доверия к содержимому для реестра контейнеров. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull

Извлечение ACR.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Извлечение или получение образов из реестра контейнеров. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>AcrPush

Отправка ACR.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Извлечение или получение образов из реестра контейнеров. |
> | Microsoft.ContainerRegistry/registries/push/write | Передача или запись образов в реестр контейнеров. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>AcrQuarantineReader

Модуль чтения данных карантина ACR.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft. ContainerRegistry/реестров/карантин/чтение | Извлечение или получение помещенных в карантин образов из реестра контейнеров. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarantineWriter

Модуль записи данных карантина ACR.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft. ContainerRegistry/реестров/карантин/чтение | Извлечение или получение помещенных в карантин образов из реестра контейнеров. |
> | Microsoft. ContainerRegistry/реестров/карантин/запись | Запись и изменение состояния карантина образов, помещенных в карантин. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Роль администратора кластера в Службе Azure Kubernetes

Список действий, выполненных с помощью учетных данных администратора кластера.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Перечисляет учетные данные clusterAdmin управляемого кластера. |
> | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Получение профиля доступа управляемого кластера по имени роли с помощью вывода учетных данных. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Роль пользователя кластера в Службе Azure Kubernetes

Список действий, выполненных с помощью учетных данных пользователя кластера.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Перечисляет учетные данные clusterUser управляемого кластера. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>Базы данных


### <a name="cosmos-db-account-reader-role"></a>Роль читателя учетных записей Cosmos DB

Позволяет считывать данные учетных записей Azure Cosmos DB. Сведения об управлении учетными записями Azure Cosmos DB см. в разделе [Участник учетной записи DocumentDB](#documentdb-account-contributor).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.DocumentDB/*/read | Чтение любой коллекции |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Считывает ключи только для чтения учетной записи базы данных. |
> | Microsoft.Insights/MetricDefinitions/read | Считывает определения метрик. |
> | Microsoft.Insights/Metrics/read | Считывает метрики. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Оператор Cosmos DB

Позволяет управлять учетными записями Azure Cosmos DB, но не доступом к данным. Предотвращает доступ к ключам учетной записи и строкам подключения.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Присоединение к подсети ресурса, например учетной записи хранения или базы данных SQL. Не оповещать. |
> | **NotActions** |  |
> | Microsoft. DocumentDB/databaseAccounts/Реадонликэйс/* |  |
> | Microsoft. DocumentDB/databaseAccounts/Реженератекэй/* |  |
> | Microsoft. DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft. DocumentDB/databaseAccounts/Листконнектионстрингс/* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

Позволяет отправлять запрос на восстановление контейнера или базы данных Cosmos DB для учетной записи.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | Отправка запроса на настройку резервного копирования |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | Отправка запроса восстановления |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>Участник учетной записи DocumentDB

Может управлять учетными записями Azure Cosmos DB Служба Azure Cosmos DB раньше называлась DocumentDB.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.DocumentDb/databaseAccounts/* | Создание учетных записей Azure Cosmos DB и управление ими |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Присоединение к подсети ресурса, например учетной записи хранения или базы данных SQL. Не оповещать. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Участник кэша Redis

Позволяет управлять кэшем Redis, но не доступом к нему.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Cache/redis/* | Создание кэшей Redis и управление ими |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>Участник БД SQL

Позволяет управлять базами данных SQL, но не доступом к ним. Кроме того, не позволяет управлять их политиками безопасности или родительскими серверами SQL Server.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | Создание баз данных SQL и управление ими |
> | Microsoft.Sql/servers/read | Возвращение списка серверов или получение свойств для указанного сервера. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | Microsoft.Insights/metrics/read | Считывает метрики. |
> | Microsoft.Insights/metricDefinitions/read | Считывает определения метрик. |
> | **NotActions** |  |
> | Microsoft. SQL/Манажединстанцес/databases/Куррентсенситивитилабелс/* |  |
> | Microsoft. SQL/Манажединстанцес/databases/Рекоммендедсенситивитилабелс/* |  |
> | Microsoft. SQL/Манажединстанцес/базы данных/схемы/таблицы/столбцы/Сенситивитилабелс/* |  |
> | Microsoft. SQL/Манажединстанцес/databases/СекуритялертполиЦиес/* |  |
> | Microsoft. SQL/Манажединстанцес/databases/Сенситивитилабелс/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/Манажединстанцес/СекуритялертполиЦиес/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Изменение политик аудита. |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Изменение параметров аудита. |
> | Microsoft.Sql/servers/databases/auditRecords/read | Извлекает записи аудита больших двоичных объектов для базы данных. |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Изменение политик подключения. |
> | Microsoft. SQL/Servers/databases/Куррентсенситивитилабелс/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Изменение политик маскирования данных. |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft. SQL/Servers/databases/Рекоммендедсенситивитилабелс/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Изменение политик оповещения системы безопасности. |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Изменение метрик безопасности. |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>Участник SQL Управляемый экземпляр

Позволяет управлять управляемыми экземплярами SQL и требуемой конфигурацией сети, но не может предоставлять доступ другим пользователям.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft. Network/networkSecurityGroups/* |  |
> | Microsoft. Network/Раутетаблес/* |  |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft. SQL/Манажединстанцес/* |  |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | Microsoft. Network/virtualNetworks/подсети/* |  |
> | Microsoft. Network/virtualNetworks/* |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Insights/metrics/read | Считывает метрики. |
> | Microsoft.Insights/metricDefinitions/read | Считывает определения метрик. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>Диспетчер безопасности SQL

Позволяет управлять политиками безопасности серверов SQL Server и баз данных SQL, но не доступом к ним.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Присоединение к подсети ресурса, например учетной записи хранения или базы данных SQL. Не оповещать. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft. SQL/Манажединстанцес/databases/Куррентсенситивитилабелс/* |  |
> | Microsoft. SQL/Манажединстанцес/databases/Рекоммендедсенситивитилабелс/* |  |
> | Microsoft. SQL/Манажединстанцес/базы данных/схемы/таблицы/столбцы/Сенситивитилабелс/* |  |
> | Microsoft. SQL/Манажединстанцес/databases/СекуритялертполиЦиес/* |  |
> | Microsoft. SQL/Манажединстанцес/databases/Сенситивитилабелс/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/Манажединстанцес/СекуритялертполиЦиес/* |  |
> | Microsoft. SQL/Манажединстанцес/databases/Транспарентдатаенкриптион/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Создание политик аудита SQL Server и управление ими |
> | Microsoft.Sql/servers/auditingSettings/* | Создание параметров аудита SQL Server и управление ими |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | Извлечение сведений о расширенной политике аудита больших двоичных объектов, настроенной на заданном сервере. |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Создание политик аудита баз данных SQL Server и управление ими |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Создание параметров аудита баз данных SQL Server и управление ими |
> | Microsoft.Sql/servers/databases/auditRecords/read | Извлекает записи аудита больших двоичных объектов для базы данных. |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Создание политик подключения баз данных SQL Server и управление ими |
> | Microsoft. SQL/Servers/databases/Куррентсенситивитилабелс/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Создание политик маскирования данных баз данных SQL Server и управление ими |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Извлечение сведений о расширенной политике аудита больших двоичных объектов, настроенной для заданной базы данных. |
> | Microsoft.Sql/servers/databases/read | Возвращение списка баз данных или возвращение свойств указанной базы данных. |
> | Microsoft. SQL/Servers/databases/Рекоммендедсенситивитилабелс/* |  |
> | Microsoft.Sql/servers/databases/schemas/read | Получение схемы базы данных. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Получение столбца базы данных. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Получение таблицы базы данных. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Создание политик оповещения системы безопасности баз данных SQL Server и управление ими |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Создание метрик безопасности базы данных SQL и управление ими |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft. SQL/Servers/databases/Транспарентдатаенкриптион/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | Возвращение списка серверов или получение свойств для указанного сервера. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Создание политик оповещения системы безопасности SQL Server и управление ими |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>Участник SQL Server

Позволяет управлять серверами и базами данных SQL, но не доступом к ним, а не с их политиками безопасности.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Создание серверов SQL Server и управление ими |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | Microsoft.Insights/metrics/read | Считывает метрики. |
> | Microsoft.Insights/metricDefinitions/read | Считывает определения метрик. |
> | **NotActions** |  |
> | Microsoft. SQL/Манажединстанцес/databases/Куррентсенситивитилабелс/* |  |
> | Microsoft. SQL/Манажединстанцес/databases/Рекоммендедсенситивитилабелс/* |  |
> | Microsoft. SQL/Манажединстанцес/базы данных/схемы/таблицы/столбцы/Сенситивитилабелс/* |  |
> | Microsoft. SQL/Манажединстанцес/databases/СекуритялертполиЦиес/* |  |
> | Microsoft. SQL/Манажединстанцес/databases/Сенситивитилабелс/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/Манажединстанцес/СекуритялертполиЦиес/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Изменение политик аудита SQL Server. |
> | Microsoft.Sql/servers/auditingSettings/* | Изменение параметров аудита SQL Server. |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Изменение политик аудита баз данных SQL Server. |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Изменение параметров аудита баз данных SQL Server. |
> | Microsoft.Sql/servers/databases/auditRecords/read | Извлекает записи аудита больших двоичных объектов для базы данных. |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Изменение политик подключения баз данных SQL Server. |
> | Microsoft. SQL/Servers/databases/Куррентсенситивитилабелс/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Изменение политик маскирования данных баз данных SQL Server. |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft. SQL/Servers/databases/Рекоммендедсенситивитилабелс/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Изменение политик оповещения системы безопасности баз данных SQL Server. |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Изменение метрик безопасности баз данных SQL Server. |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Изменение политик оповещения системы безопасности SQL Server. |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>Analytics


### <a name="azure-event-hubs-data-owner"></a>Владелец данных концентраторов событий Azure

Обеспечивает полный доступ к ресурсам концентраторов событий Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft. EventHub/* |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft. EventHub/* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Приемник данных концентраторов событий Azure

Разрешает получать доступ к ресурсам концентраторов событий Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft. EventHub/*/евенсубс/консумерграупс/Реад |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft. EventHub/*/рецеиве/Актион |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Отправитель данных концентраторов событий Azure

Разрешает отправку доступа к ресурсам концентраторов событий Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft. EventHub/*/евенсубс/Реад |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft. EventHub/*/Сенд/Актион |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>Участник фабрики данных

Создание фабрик данных и управление ими, а также их дочерними ресурсами.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.DataFactory/dataFactories/* | Создание фабрик данных и дочерних ресурсов внутри их, а также управление ими. |
> | Microsoft.DataFactory/factories/* | Создание фабрик данных и дочерних ресурсов внутри их, а также управление ими. |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | Microsoft.EventGrid/eventSubscriptions/write | Создание или обновление подписки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>Средство очистки данных

Может выполнить очистку данных аналитики.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Очистка данных Application Insights. |
> | Microsoft.OperationalInsights/workspaces/*/read | Просмотр данных log Analytics |
> | Microsoft.OperationalInsights/workspaces/purge/action | Удаление указанных данных из рабочей области. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>Оператор кластера HDInsight

Позволяет читать и изменять конфигурации кластера HDInsight.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft. HDInsight/*/Реад |  |
> | Microsoft. HDInsight/Clusters/Жетгатевайсеттингс/действие | Получение параметров шлюза для кластера HDInsight |
> | Microsoft. HDInsight/Clusters/Упдатегатевайсеттингс/действие | Обновление параметров шлюза для кластера HDInsight |
> | Microsoft. HDInsight/кластеры/конфигурации/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Resources/deployments/operations/read | Возвращает операции развертывания или выводит их список. |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>Участник доменных служб HDInsight

Позволяет читать, создавать, изменять и удалять операции, связанные с доменными службами, необходимыми для Корпоративного пакета безопасности HDInsight

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.AAD/*/read |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>участник Log Analytics.

Участник Log Analytics может считывать все данные мониторинга и изменять параметры мониторинга. Изменение параметров мониторинга подразумевает добавление расширений в виртуальные машины, чтение ключей учетной записи хранения для настройки коллекции журналов в службе хранилища Azure, создание и настройку учетных записей службы автоматизации, добавление решений и настройку диагностики Azure во всех ресурсах Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Отображает ключи доступа для учетных записей хранения. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft. Хибридкомпуте/Machines/Extensions/Write | Устанавливает или обновляет расширения Arc Azure. |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Insights/diagnosticSettings/* | Создание, обновление или считывание параметра диагностики для сервера анализа данных. |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>читатель Log Analytics;

Читатель Log Analytics может просматривать все данные мониторинга, выполнять по ним поиск и просматривать параметры мониторинга, в том числе конфигурацию системы диагностики Azure для всех ресурсов Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Поиск с помощью нового механизма. |
> | Microsoft.OperationalInsights/workspaces/search/action | Выполняет поисковый запрос. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Извлекает открытые ключи для рабочей области. Эти ключи используются для подключения агентов Microsoft Operational Insights к рабочей области. |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>Блокчейн


### <a name="blockchain-member-node-access-preview"></a>Доступ к узлу-члену блокчейн (Предварительная версия)

Разрешает доступ к узлам члена Блокчейн

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft. Блокчейн/Блоккчаинмемберс/Трансактионнодес/Read | Возвращает или перечисляет существующие узлы транзакций с элементами Блокчейн. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft. Блокчейн/Блоккчаинмемберс/Трансактионнодес/Connect/Action | Подключается к узлу транзакции-члена Блокчейн. |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>Искусственный интеллект и машинное обучение


### <a name="cognitive-services-contributor"></a>Участник служб Cognitive Services

Позволяет создавать, читать, обновлять, удалять ключи служб Cognitive Services и управлять ими.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | Возвращает функции подписки. |
> | Microsoft.Features/providers/features/read | Возвращает функцию подписки в заданном поставщике ресурсов. |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Insights/diagnosticSettings/* | Создание, обновление или считывание параметра диагностики для сервера анализа данных. |
> | Microsoft.Insights/logDefinitions/read | Считывает определения журналов. |
> | Microsoft.Insights/metricdefinitions/read | Считывает определения метрик. |
> | Microsoft.Insights/metrics/read | Считывает метрики. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/deployments/operations/read | Возвращает операции развертывания или выводит их список. |
> | Microsoft.Resources/subscriptions/operationresults/read | Возвращает результаты операции подписки. |
> | Microsoft.Resources/subscriptions/read | Возвращает список подписок. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Cognitive Services модуль чтения данных (Предварительная версия)

Позволяет считывать данные Cognitive Services.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | *отсутствуют* |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Пользователь служб Cognitive Services

Позволяет создавать и читать список ключей служб Cognitive Services.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | Отображает ключи. |
> | Microsoft.Insights/alertRules/read | Чтение классического оповещения метрики. |
> | Microsoft.Insights/diagnosticSettings/read | Чтение параметра диагностики для ресурсов. |
> | Microsoft.Insights/logDefinitions/read | Считывает определения журналов. |
> | Microsoft.Insights/metricdefinitions/read | Считывает определения метрик. |
> | Microsoft.Insights/metrics/read | Считывает метрики. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/operations/read | Возвращает операции развертывания или выводит их список. |
> | Microsoft.Resources/subscriptions/operationresults/read | Возвращает результаты операции подписки. |
> | Microsoft.Resources/subscriptions/read | Возвращает список подписок. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>Смешанная реальность


### <a name="spatial-anchors-account-contributor"></a>Участник учетной записи пространственных привязок

Позволяет управлять пространственными привязками в учетной записи, но не удалять их

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | *отсутствуют* |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/создание/действие | Создание пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/обнаружение/чтение | Обнаружение ближайших пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/свойства/чтение | Получение свойств пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/запрос/чтение | Определение пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/субмитдиаг/Read | Отправка диагностических данных для повышения качества службы пространственных привязок Azure |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/запись | Обновить свойства пространственных привязок |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>Владелец учетной записи пространственных привязок

Позволяет управлять пространственными привязками в учетной записи, включая их удаление.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | *отсутствуют* |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/создание/действие | Создание пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/Delete | Удаление пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/обнаружение/чтение | Обнаружение ближайших пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/свойства/чтение | Получение свойств пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/запрос/чтение | Определение пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/субмитдиаг/Read | Отправка диагностических данных для повышения качества службы пространственных привязок Azure |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/запись | Обновить свойства пространственных привязок |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>Читатель учетной записи пространственных привязок

Позволяет нахождение и чтение свойств пространственных привязок в учетной записи.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | *отсутствуют* |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/обнаружение/чтение | Обнаружение ближайших пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/свойства/чтение | Получение свойств пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/запрос/чтение | Определение пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/субмитдиаг/Read | Отправка диагностических данных для повышения качества службы пространственных привязок Azure |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>Интеграция


### <a name="api-management-service-contributor"></a>Участник службы управления API

Может управлять службой и интерфейсами API.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.ApiManagement/service/* | Создание службы управления API и управление ею |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>Роль оператора службы управления API

Может управлять службой, но не интерфейсами API.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.ApiManagement/service/*/read | Чтение экземпляров службы управления API. |
> | Microsoft.ApiManagement/service/backup/action | Архивирует службу управления API в указанный контейнер в предоставленной пользователем учетной записи хранения. |
> | Microsoft.ApiManagement/service/delete | Удаляет экземпляр службы управления API. |
> | Microsoft.ApiManagement/service/managedeployments/action | Позволяет изменить номер SKU или единицы, а также добавить или удалить региональные развертывания службы управления API. |
> | Microsoft.ApiManagement/service/read | Чтение метаданных для экземпляра службы управления API. |
> | Microsoft.ApiManagement/service/restore/action | Восстановление службы управления API из указанного контейнера в предоставленной пользователем учетной записи хранения. |
> | Microsoft.ApiManagement/service/updatecertificate/action | Отправка сертификата TLS/SSL для службы управления API |
> | Microsoft.ApiManagement/service/updatehostname/action | Позволяет настроить, обновить или удалить имена личных доменов для службы управления API. |
> | Microsoft.ApiManagement/service/write | Создание или обновление экземпляра службы управления API |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Получение ключей, связанных с пользователем |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>Роль читателя данных службы управления API

Доступ к службе и интерфейсам API в режиме "только для чтения".

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.ApiManagement/service/*/read | Чтение экземпляров службы управления API. |
> | Microsoft.ApiManagement/service/read | Чтение метаданных для экземпляра службы управления API. |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Получение ключей, связанных с пользователем |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-owner"></a>Владелец данных конфигурации приложения

Разрешает полный доступ к данным конфигурации приложения.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | *отсутствуют* |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft. Аппконфигуратион/Конфигуратионсторес/*/Реад |  |
> | Microsoft. Аппконфигуратион/Конфигуратионсторес/*/Врите |  |
> | Microsoft. Аппконфигуратион/Конфигуратионсторес/*/Delete |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>Модуль чтения данных конфигурации приложений

Разрешает доступ на чтение к данным конфигурации приложения.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | *отсутствуют* |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft. Аппконфигуратион/Конфигуратионсторес/*/Реад |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Владелец данных служебной шины Azure

Обеспечивает полный доступ к ресурсам служебной шины Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft. ServiceBus/* |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft. ServiceBus/* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Приемник данных служебной шины Azure

Разрешает получать доступ к ресурсам служебной шины Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft. ServiceBus/*/куеуес/Реад |  |
> | Microsoft. ServiceBus/*/топикс/Реад |  |
> | Microsoft. ServiceBus/*/топикс/субскриптионс/Реад |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft. ServiceBus/*/рецеиве/Актион |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Отправитель данных служебной шины Azure

Разрешает отправку доступа к ресурсам служебной шины Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft. ServiceBus/*/куеуес/Реад |  |
> | Microsoft. ServiceBus/*/топикс/Реад |  |
> | Microsoft. ServiceBus/*/топикс/субскриптионс/Реад |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft. ServiceBus/*/Сенд/Актион |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Владелец регистрации Azure Stack

Позволяет управлять регистрациями Azure Stack.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft. AzureStack/регистрации/Products/*/Action |  |
> | Microsoft.AzureStack/registrations/products/read | Получает свойства продукта из Azure Stack Marketplace. |
> | Microsoft.AzureStack/registrations/read | Получает свойства регистрации Azure Stack. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-contributor"></a>Участник EventGrid EventSubscription

Позволяет управлять операциями с подписками на события Сетки событий.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.EventGrid/eventSubscriptions/* |  |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Создание списка подписок на глобальные события по типу темы |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Создание списка подписок на события в регионе |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Создание списка подписок на события в регионе по типу темы |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>Читатель EventGrid EventSubscription

Позволяет получить доступ на чтение к подпискам на события Сетки событий.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.EventGrid/eventSubscriptions/read | Чтение подписки |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Создание списка подписок на глобальные события по типу темы |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Создание списка подписок на события в регионе |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Создание списка подписок на события в регионе по типу темы |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Участник учетной записи интеллектуальных систем

Позволяет управлять учетными записями интеллектуальных систем, но не доступом к ним.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.IntelligentSystems/accounts/* | Создание учетных записей интеллектуальных систем и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>Создатель приложений логики

Позволяет управлять приложениями логики, но не изменяет доступ к ним.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Отображает ключи доступа для учетных записей хранения. |
> | Microsoft.ClassicStorage/storageAccounts/read | Возвращает учетную запись хранения для заданной учетной записи. |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft. Insights/Метрикалертс/* |  |
> | Microsoft.Insights/diagnosticSettings/* | Создание, обновление или считывание параметра диагностики для сервера анализа данных. |
> | Microsoft.Insights/logdefinitions/* | Это разрешение необходимо пользователям, которым требуется доступ к журналам действия на портале. Получение списка категорий журнала в журнале действий. |
> | Microsoft.Insights/metricDefinitions/* | Чтение определений метрик (вывод списка доступных типов метрик для ресурса). |
> | Microsoft.Logic/* | Управляет ресурсами Logic Apps. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/operationresults/read | Возвращает результаты операции подписки. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
> | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | Microsoft.Web/connectionGateways/* | Создает шлюз подключения и управляет им. |
> | Microsoft.Web/connections/* | Создает подключение и управляет им. |
> | Microsoft.Web/customApis/* | Создает настраиваемый API и управляет им. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Возвращает свойства плана службы приложений. |
> | Microsoft.Web/sites/functions/listSecrets/action | Перечисление секретов функций. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>Оператор приложений логики

Позволяет читать, включать и отключать приложения логики, но не изменять и не обновлять их.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/*/read | Считывание правил оповещений Insights. |
> | Microsoft. Insights/Метрикалертс/*/Реад |  |
> | Microsoft.Insights/diagnosticSettings/*/read | Получает параметры диагностики для Logic Apps. |
> | Microsoft.Insights/metricDefinitions/*/read | Получает доступные метрики для Logic Apps. |
> | Microsoft.Logic/*/read | Считывает ресурсы Logic Apps. |
> | Microsoft.Logic/workflows/disable/action | Отключает рабочий процесс. |
> | Microsoft.Logic/workflows/enable/action | Включает рабочий процесс. |
> | Microsoft.Logic/workflows/validate/action | Проверяет рабочий процесс. |
> | Microsoft.Resources/deployments/operations/read | Возвращает операции развертывания или выводит их список. |
> | Microsoft.Resources/subscriptions/operationresults/read | Возвращает результаты операции подписки. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | Microsoft.Web/connectionGateways/*/read | Считывает шлюзы подключения. |
> | Microsoft.Web/connections/*/read | Считывает подключения. |
> | Microsoft.Web/customApis/*/read | Считывает настраиваемый API. |
> | Microsoft.Web/serverFarms/read | Возвращает свойства плана службы приложений. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>Удостоверение


### <a name="managed-identity-contributor"></a>Участник управляемого удостоверения

Создание, чтение, обновление и удаление пользовательских удостоверений.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/read | Получение существующего пользовательского удостоверения. |
> | Microsoft.ManagedIdentity/userAssignedIdentities/write | Создание существующего пользовательского удостоверения или обновление связанных с ним тегов. |
> | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Удаление существующего пользовательского удостоверения. |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>Оператор управляемого удостоверения

Чтение и назначение пользовательских удостоверений.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>Безопасность


### <a name="azure-sentinel-contributor"></a>Участник Sentinel Azure

Участник Sentinel Azure

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft. Секуритинсигхтс/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Поиск с помощью нового механизма. |
> | Microsoft.OperationalInsights/workspaces/*/read | Просмотр данных log Analytics |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* |  |
> | Microsoft.OperationsManagement/solutions/read | Возвращает существующее решение OMS. |
> | Microsoft.OperationalInsights/workspaces/query/read | Выполнение запросов к данным в рабочей области |
> | Microsoft. OperationalInsights/рабочие области/запрос/*/Реад |  |
> | Microsoft. OperationalInsights/рабочие области/источники данных/чтение | Возвращает источники данных в рабочей области. |
> | Microsoft. Insights/книги/* |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-reader"></a>Читатель маркеров Azure

Читатель маркеров Azure

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft. Секуритинсигхтс/*/Реад |  |
> | Microsoft. Секуритинсигхтс/Датаконнекторсчеккрекуирементс/действие | Проверка авторизации и лицензии пользователя |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Поиск с помощью нового механизма. |
> | Microsoft.OperationalInsights/workspaces/*/read | Просмотр данных log Analytics |
> | Microsoft. OperationalInsights/рабочие области/LinkedServices/чтение | Получение связанных служб в заданной рабочей области. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | Возвращает сохраненный поисковый запрос. |
> | Microsoft.OperationsManagement/solutions/read | Возвращает существующее решение OMS. |
> | Microsoft.OperationalInsights/workspaces/query/read | Выполнение запросов к данным в рабочей области |
> | Microsoft. OperationalInsights/рабочие области/запрос/*/Реад |  |
> | Microsoft. OperationalInsights/рабочие области/источники данных/чтение | Возвращает источники данных в рабочей области. |
> | Microsoft. Insights/книги/чтение | Чтение книги |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-responder"></a>Ответчик Sentinel Azure

Ответчик Sentinel Azure

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft. Секуритинсигхтс/*/Реад |  |
> | Microsoft. Секуритинсигхтс/Датаконнекторсчеккрекуирементс/действие | Проверка авторизации и лицензии пользователя |
> | Microsoft. Секуритинсигхтс/cases/* |  |
> | Microsoft. Секуритинсигхтс/происшествия/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Поиск с помощью нового механизма. |
> | Microsoft.OperationalInsights/workspaces/*/read | Просмотр данных log Analytics |
> | Microsoft. OperationalInsights/рабочие области/источники данных/чтение | Возвращает источники данных в рабочей области. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | Возвращает сохраненный поисковый запрос. |
> | Microsoft.OperationsManagement/solutions/read | Возвращает существующее решение OMS. |
> | Microsoft.OperationalInsights/workspaces/query/read | Выполнение запросов к данным в рабочей области |
> | Microsoft. OperationalInsights/рабочие области/запрос/*/Реад |  |
> | Microsoft. OperationalInsights/рабочие области/источники данных/чтение | Возвращает источники данных в рабочей области. |
> | Microsoft. Insights/книги/чтение | Чтение книги |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Участник Key Vault

Позволяет управлять хранилищами ключей, но не доступом к ним.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Очищает обратимо удаленное хранилище Key Vault. |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>администратор безопасности;

Просмотр и обновление разрешений для центра безопасности. Те же разрешения, что и у роли читателя безопасности, также могут обновлять политику безопасности и закрывать предупреждения и рекомендации.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Authorization/policyAssignments/* | Создание назначений политик и управление ими |
> | Microsoft.Authorization/policyDefinitions/* | Создание определений политик и управление ими |
> | Microsoft.Authorization/policySetDefinitions/* | Создание наборов политик и управление ими |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Management/managementGroups/read | Вывод списка групп управления для пользователя, прошедшего проверку подлинности. |
> | Microsoft.operationalInsights/workspaces/*/read | Просмотр данных log Analytics |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Security/* | Создание компонентов и политик безопасности и управление ими |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>Участник оценки безопасности

Позволяет отправлять оценки в центр безопасности

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft. Security/оценки/запись | Создание или обновление оценок безопасности в подписке |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>Диспетчер безопасности (устаревший)

Это устаревшая роль. Используйте вместо него администратора безопасности.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.ClassicCompute/*/read | Чтение сведений о конфигурации классических виртуальных машин |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Запись сведений о конфигурации классических виртуальных машин |
> | Microsoft.ClassicNetwork/*/read | Чтение сведений о конфигурации для классической сети |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Security/* | Создание компонентов и политик безопасности и управление ими |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>Читатель сведений о безопасности

Просмотр разрешений для центра безопасности. Может просматривать рекомендации, предупреждения, политику безопасности и состояния безопасности, но не может вносить изменения.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.operationalInsights/workspaces/*/read | Просмотр данных log Analytics |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Security/*/read | Чтение компонентов и политик безопасности |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | Microsoft.Management/managementGroups/read | Вывод списка групп управления для пользователя, прошедшего проверку подлинности. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>Пользователь DevTest Labs

Позволяет подключать, запускать, перезапускать виртуальные машины и завершать их работу в Azure DevTest Labs.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Compute/availabilitySets/read | Возвращает свойства группы доступности. |
> | Microsoft.Compute/virtualMachines/*/read | Чтение свойств виртуальной машины (размеры виртуальных машин, состояние среды выполнения, расширения виртуальных машин, и т. д.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Завершает работу виртуальной машины и освобождает ее вычислительные ресурсы. |
> | Microsoft.Compute/virtualMachines/read | Возвращает свойства виртуальной машины. |
> | Microsoft.Compute/virtualMachines/restart/action | Перезапускает виртуальную машину. |
> | Microsoft.Compute/virtualMachines/start/action | Запуск виртуальной машины |
> | Microsoft.DevTestLab/*/read | Чтение свойств лаборатории |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Отправляет заявку на доступ к случайной запрашиваемой виртуальной машине в лаборатории. |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Создает виртуальные машины в лаборатории. |
> | Microsoft. DevTestLab/Labs/Енсурекуррентусерпрофиле/действие | Убедитесь, что текущий пользователь имеет допустимый профиль в лаборатории. |
> | Microsoft.DevTestLab/labs/formulas/delete | Удаляет формулы. |
> | Microsoft.DevTestLab/labs/formulas/read | Считывает формулы. |
> | Microsoft.DevTestLab/labs/formulas/write | Добавляет или изменяет формулы. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Оценивает политику лаборатории. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Присваивает владение существующей виртуальной машиной. |
> | Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action | Выводит список применимых расписаний запуска и остановки, если они есть. |
> | Microsoft. DevTestLab/Labs/virtualMachines/Жетрдпфилеконтентс/действие | Получает строку, которая представляет содержание RDP-файла для виртуальной машины. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Присоединяет внутренний пул адресов подсистемы балансировки нагрузки. Не оповещать. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Присоединяет правило NAT для входящего трафика подсистемы балансировки нагрузки. Не оповещать. |
> | Microsoft.Network/networkInterfaces/*/read | Чтение свойств сетевого интерфейса (например, всех балансировщиков нагрузки, частью которых является сетевой интерфейс) |
> | Microsoft.Network/networkInterfaces/join/action | Присоединяет виртуальную машину к сетевому интерфейсу. Не оповещать. |
> | Microsoft.Network/networkInterfaces/read | Возвращает определение сетевого интерфейса.  |
> | Microsoft.Network/networkInterfaces/write | Создает новый сетевой интерфейс или обновляет существующий.  |
> | Microsoft.Network/publicIPAddresses/*/read | Чтение свойств общедоступного IP-адреса |
> | Microsoft.Network/publicIPAddresses/join/action | Присоединяет общедоступный IP-адрес. Не оповещать. |
> | Microsoft.Network/publicIPAddresses/read | Возвращает определение общедоступного IP-адреса. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Присоединяет виртуальную сеть. Не оповещать. |
> | Microsoft.Resources/deployments/operations/read | Возвращает операции развертывания или выводит их список. |
> | Microsoft.Resources/deployments/read | Возвращает развернутые службы или выводит их список. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Выводит список доступных размеров для обновления виртуальной машины. |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>Создатель лаборатории

Позволяет создавать, администрировать и удалять управляемые лаборатории в учетных записях Служб лабораторий Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Создание лаборатории в учетной записи лаборатории. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Позволяет получать сведения о доступности по регионам для каждой категории размеров, настроенной в учетной записи лаборатории. |
> | Microsoft. Лабсервицес/Лабаккаунтс/ЖетприЦингандаваилабилити/Action | Получите сведения о ценах и доступности комбинаций размеров, географических регионов и операционных систем для учетной записи лаборатории. |
> | Microsoft. Лабсервицес/Лабаккаунтс/Жетрестриктионсандусаже/Action | Получить базовые ограничения и использование для этой подписки |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, manage, delete your managed labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action",
        "Microsoft.LabServices/labAccounts/getRegionalAvailability/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="monitor"></a>Монитор


### <a name="application-insights-component-contributor"></a>Участник компонента Application Insights

Может управлять компонентами Application Insights

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание классических правил генерации оповещений и управление ими |
> | Microsoft. Insights/Метрикалертс/* | Создание новых правил генерации оповещений и управление ими |
> | Microsoft.Insights/components/* | Создание компонентов Insights и управление ими |
> | Microsoft.Insights/webtests/* | Создание веб-тестов аналитики и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Отладчик моментальных снимков Application Insights

Пользователю предоставляется разрешение на просмотр и загрузку моментальных снимков отладки, собранных с помощью Application Insights Snapshot Debugger. Обратите внимание, что эти разрешения не включены в роли [Владелец](#owner) или [Участник](#contributor).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>Monitoring Contributor

Может читать все данные мониторинга и изменять параметры мониторинга. Ознакомьтесь также со статьей [Приступая к работе с ролями, разрешениями и системой безопасности с помощью Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Insights/components/* | Создание компонентов Insights и управление ими |
> | Microsoft.Insights/DiagnosticSettings/* | Создание, обновление или считывание параметра диагностики для сервера анализа данных. |
> | Microsoft.Insights/eventtypes/* | Вывод списка событий журнала действий (событий управления) в подписке. Это разрешение применяется для доступа к журналу действий посредством кода или портала. |
> | Microsoft.Insights/LogDefinitions/* | Это разрешение необходимо пользователям, которым требуется доступ к журналам действия на портале. Получение списка категорий журнала в журнале действий. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Чтение определений метрик (вывод списка доступных типов метрик для ресурса). |
> | Microsoft.Insights/Metrics/* | Чтение метрик для ресурса. |
> | Microsoft.Insights/Register/Action | Регистрирует поставщик Microsoft Insights. |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Создание веб-тестов аналитики и управление ими |
> | Microsoft. Insights/книги/* |  |
> | Microsoft. Insights/Привателинкскопес/* |  |
> | Microsoft. Insights/Привателинкскопеоператионстатусес/* |  |
> | Microsoft.OperationalInsights/workspaces/write | Создает новую рабочую область или устанавливает связь с существующей рабочей областью с помощью указанного идентификатора клиента, полученного из нее. |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Чтение, запись и удаление пакетов решений log Analytics. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Чтение, запись и удаление сохраненных поисков log Analytics. |
> | Microsoft.OperationalInsights/workspaces/search/action | Выполняет поисковый запрос. |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Извлекает открытые ключи для рабочей области. Эти ключи используются для подключения агентов Microsoft Operational Insights к рабочей области. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Чтение, запись и удаление конфигураций аналитики хранилища log Analytics. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | Microsoft.WorkloadMonitor/monitors/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | Microsoft. Алертсманажемент/Смартдетекторалертрулес/* |  |
> | Microsoft. Алертсманажемент/Актионрулес/* |  |
> | Microsoft. Алертсманажемент/Смартграупс/* |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/privateLinkScopes/*",
        "Microsoft.Insights/privateLinkScopeOperationStatuses/*",
        "Microsoft.OperationalInsights/workspaces/write",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.WorkloadMonitor/notificationSettings/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>Издатель метрик мониторинга

Включает публикацию метрик ресурсов Azure

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Insights/Register/Action | Регистрирует поставщик Microsoft Insights. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft.Insights/Metrics/Write | Запись метрик. |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>Роль Monitoring Reader

Может читать все данные мониторинга (метрики, журналы и т. д.). Ознакомьтесь также со статьей [Приступая к работе с ролями, разрешениями и системой безопасности с помощью Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | Microsoft.OperationalInsights/workspaces/search/action | Выполняет поисковый запрос. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>Участник книги

Может сохранять общие книги.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft. Insights/книги/запись | Создание или обновление книги |
> | Microsoft. Insights/книги/удаление | Удаление книги |
> | Microsoft. Insights/книги/чтение | Чтение книги |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>Читатель книг

Может считывать книги.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft. Insights/книги/чтение | Чтение книги |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>Управление + руководство


### <a name="automation-job-operator"></a>Оператор заданий службы автоматизации

Создание заданий и управление ими с помощью модулей Runbook службы автоматизации.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Считывает ресурсы гибридной рабочей роли Runbook. |
> | Microsoft.Automation/automationAccounts/jobs/read | Возвращает задание службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Возобновляет выполнение задания службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Останавливает задание службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Возвращает поток задания службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Приостанавливает задание службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobs/write | Создает задание службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Возвращает выходные данные задания. |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>Оператор службы автоматизации

Операторы автоматизации могут запускать, останавливать, приостанавливать и возобновлять задания.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Считывает ресурсы гибридной рабочей роли Runbook. |
> | Microsoft.Automation/automationAccounts/jobs/read | Возвращает задание службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Возобновляет выполнение задания службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Останавливает задание службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Возвращает поток задания службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Приостанавливает задание службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobs/write | Создает задание службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Возвращает расписание заданий службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Создает расписание заданий службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Получает рабочую область, связанную с учетной записью службы автоматизации. |
> | Microsoft.Automation/automationAccounts/read | Возвращает учетную запись службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/runbooks/read | Возвращает runbook службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/schedules/read | Возвращает ресурс расписания службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/schedules/write | Создает или обновляет ресурс расписания службы автоматизации Azure. |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Возвращает выходные данные задания. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>Оператор Runbook службы автоматизации

Чтение свойств Runbook, позволяющее создавать задания Runbook.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Automation/automationAccounts/runbooks/read | Возвращает runbook службы автоматизации Azure. |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-onboarding"></a>Подключение к подключенному компьютеру Azure

Может подключить подключенные к Azure компьютеры.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft. Хибридкомпуте/Machines/Read | Чтение всех машин ARC в Azure |
> | Microsoft. Хибридкомпуте/Machines/Write | Запись машин ARC в Azure |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Получение назначения конфигурации гостевой системы. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Администратор ресурсов подключенного компьютера Azure

Может читать, записывать, удалять и повторно использовать подключенные к Azure компьютеры.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft. Хибридкомпуте/Machines/Read | Чтение всех машин ARC в Azure |
> | Microsoft. Хибридкомпуте/Machines/Write | Запись машин ARC в Azure |
> | Microsoft. Хибридкомпуте/Machines/Delete | Удаление компьютеров ARC в Azure |
> | Microsoft. Хибридкомпуте/Machines/Reconnect/действие | Повторное подключение компьютеров ARC в Azure |
> | Microsoft. Хибридкомпуте/Machines/Extensions/Write | Устанавливает или обновляет расширения Arc Azure. |
> | Microsoft. Хибридкомпуте/*/Реад |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/reconnect/action",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>Читатель счетов

Разрешает читать данные выставления счетов.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Billing/*/read | Чтение сведений о выставлении счетов |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | Вывод списка групп управления для пользователя, прошедшего проверку подлинности. |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>Участник схемы

Позволяет управлять определениями схем, но не назначать их.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft. чертеж/чертежи/* | Создание определений схем или артефактов схемы и управление ими. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>Оператор схемы

Может назначать существующие опубликованные проекты, но не может создавать новые. Обратите внимание, что это работает только в том случае, если назначение выполняется с управляемым удостоверением, назначенным пользователем.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft. чертеж/Блуепринтассигнментс/* | Создание назначений схем и управление ими. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>Участник службы "Управление затратами"

Позволяет просматривать расходы и управлять конфигурацией затрат (например, бюджеты, экспорты)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Consumption/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft.Billing/billingPeriods/read |  |
> | Microsoft.Resources/subscriptions/read | Возвращает список подписок. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | Microsoft.Advisor/configurations/read | Получение конфигураций |
> | Microsoft.Advisor/recommendations/read | Считывает рекомендации. |
> | Microsoft.Management/managementGroups/read | Вывод списка групп управления для пользователя, прошедшего проверку подлинности. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>Читатель службы "Управление затратами"

Позволяет просматривать данные о расходах и конфигурации (например, бюджеты, экспорты)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Billing/billingPeriods/read |  |
> | Microsoft.Resources/subscriptions/read | Возвращает список подписок. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | Microsoft.Advisor/configurations/read | Получение конфигураций |
> | Microsoft.Advisor/recommendations/read | Считывает рекомендации. |
> | Microsoft.Management/managementGroups/read | Вывод списка групп управления для пользователя, прошедшего проверку подлинности. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>Роль участника управляемого приложения

Позволяет создавать ресурсы управляемых приложений.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | Microsoft. Solutions/Applications/* |  |
> | Microsoft.Solutions/register/action | Регистрация в решениях. |
> | Microsoft. Resources/Subscriptions/resourceGroups/* |  |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>Роль оператора управляемого приложения

Позволяет читать и выполнять действия с ресурсами управляемого приложения.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | Microsoft.Solutions/applications/read | Извлечение списка приложений. |
> | Microsoft. Solutions/*/Action |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>Читатель Управляемых приложений

Позволяет считывать ресурсы в управляемом приложении и запрашивать доступ JIT.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Solutions/jitRequests/* |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>Удаление роли назначения регистрации управляемых служб

Роль удаления назначения регистрации управляемых служб позволяет управлять пользователями клиента для удаления назначения регистрации, назначенного их клиенту.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft. ManagedServices/Регистратионассигнментс/Read | Возвращает список назначений регистрации управляемых служб. |
> | Microsoft. ManagedServices/Регистратионассигнментс/Delete | Удаляет назначение регистрации управляемых служб. |
> | Microsoft. ManagedServices/Оператионстатусес/Read | Считывает состояние операции ресурса. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>Участник группы управления

Роль участника группы управления

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Management/managementGroups/delete | Удаление группы управления. |
> | Microsoft.Management/managementGroups/read | Вывод списка групп управления для пользователя, прошедшего проверку подлинности. |
> | Microsoft.Management/managementGroups/subscriptions/delete | Отмена связывания подписки с группой управления. |
> | Microsoft.Management/managementGroups/subscriptions/write | Связывание существующей подписки с группой управления. |
> | Microsoft.Management/managementGroups/write | Создание или обновление группы управления. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>Читатель группы управления

Роль читателя группы управления

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Management/managementGroups/read | Вывод списка групп управления для пользователя, прошедшего проверку подлинности. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>Участник учетной записи New Relic APM

Позволяет управлять учетными записями и приложениями New Relic Application Performance Management, но не доступом к ним.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>Средство записи данных Policy Insights (Предварительная версия)

Разрешает доступ на чтение к политикам ресурсов и доступ на запись к событиям политики компонента ресурсов.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft. Authorization/полициассигнментс/чтение | Возвращает сведения о назначении политики. |
> | Microsoft. Authorization/PolicyDefinitions/чтение | Возвращает сведения об определении политики. |
> | Microsoft. Authorization/полицисетдефинитионс/чтение | Получение сведений об определении набора политик. |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | Microsoft. Полициинсигхтс/Чеккдатаполицикомплианце/действие | Проверьте состояние соответствия данного компонента политикам данных. |
> | Microsoft. Полициинсигхтс/Полициевентс/Логдатаевентс/Action | Регистрация событий политики компонента ресурсов. |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>Участник политики ресурсов

Пользователи с правами на создание и изменение политики ресурсов, создание запроса в службу поддержки и чтение ресурсов и иерархии.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | Microsoft.Authorization/policyassignments/* | Создание назначений политик и управление ими |
> | Microsoft.Authorization/policydefinitions/* | Создание определений политик и управление ими |
> | Microsoft.Authorization/policysetdefinitions/* | Создание наборов политик и управление ими |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Участник Site Recovery

Позволяет управлять службой Site Recovery, за исключением создания хранилищ и назначения ролей.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp является внутренней операцией, используемой службой. |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocatedStamp является внутренней операцией, используемой службой. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Операция обновления сертификата ресурса обновляет сертификат учетных данных для ресурса или хранилища. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Создание расширенных сведений, связанных с хранилищем, и управление ими |
> | Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Создание зарегистрированных удостоверений и управление ими |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Создание или обновление параметров оповещения репликации |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Считывание события |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Создание структур репликации и управление ими |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Создание заданий репликации и управление ими |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Создание политик репликации и управление ими |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Создание планов восстановления и управление ими |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Создание конфигурации службы хранилища для хранилища служб восстановления и управление ею |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Возвращает данные об использовании хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Операцию получения маркера хранилища можно использовать, чтобы получить маркер хранилища для операций серверной части уровня хранилища. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Чтение оповещений для хранилища служб восстановления |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
> | Microsoft. RecoveryServices/хранилища/Репликатионоператионстатус/чтение | Чтение состояния операции репликации хранилища |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Оператор Site Recovery

Позволяет выполнять отработку отказа и восстановление размещения, но не другие операции управления Site Recovery.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp является внутренней операцией, используемой службой. |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocatedStamp является внутренней операцией, используемой службой. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Операцию получения результатов операции можно использовать, чтобы получить состояние и результат асинхронно отправленной операции. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Операцию получения контейнеров можно использовать для получения контейнеров, зарегистрированных для ресурса. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Считывание параметров оповещений |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Считывание события |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Проверяет согласованность структуры. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Считывание структуры |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Повторно задает связь шлюза. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Обновление сертификата для структуры. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Считывание сети |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Считывание сетевых сопоставлений |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Считывание контейнеров защиты |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Считывает защищаемые элементы. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Применяет точку восстановления. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Выполняет отработку отказа с фиксацией. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Выполняет плановую отработку отказа. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Считывание защищенных элементов |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Считывает точки восстановления для репликации. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Исправляет репликацию. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Повторно защищает защищенный элемент. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Переключает контейнер защиты. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Тестирование отработки отказа |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Тестирует очистку отработки отказа. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Отработка отказа |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Обновляет службу Mobility Service. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Считывание сопоставлений контейнера защиты |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Считывание поставщиков службы восстановления |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Обновляет поставщик. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Считывание классификации хранилища |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Считывание сопоставлений классификаций хранилища |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Чтение vCenter |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Создание заданий репликации и управление ими |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Считывание политики |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Выполняет отработку отказа с фиксацией для плана восстановления. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Выполняет плановую отработку отказа для плана восстановления. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Считывание планов восстановления |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Повторно защищает план восстановления. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Тестирует отработку отказа для плана восстановления. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Тестирует очистку отработки отказа для плана восстановления. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Выполняет отработку отказа для плана восстановления. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Чтение оповещений для хранилища служб восстановления |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Возвращает данные об использовании хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Операцию получения маркера хранилища можно использовать, чтобы получить маркер хранилища для операций серверной части уровня хранилища. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Читатель Site Recovery

Позволяет просматривать состояние Site Recovery без выполнения других операций управления.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp является внутренней операцией, используемой службой. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Возвращает оповещения для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Операцию получения результатов операции можно использовать, чтобы получить состояние и результат асинхронно отправленной операции. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Операцию получения контейнеров можно использовать для получения контейнеров, зарегистрированных для ресурса. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Считывание параметров оповещений |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Считывание события |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Считывание структуры |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Считывание сети |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Считывание сетевых сопоставлений |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Считывание контейнеров защиты |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Считывает защищаемые элементы. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Считывание защищенных элементов |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Считывает точки восстановления для репликации. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Считывание сопоставлений контейнера защиты |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Считывание поставщиков службы восстановления |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Считывание классификации хранилища |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Считывание сопоставлений классификаций хранилища |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Чтение vCenter |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Считывание задания |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Считывание политики |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Считывание планов восстановления |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Возвращает данные об использовании хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Операцию получения маркера хранилища можно использовать, чтобы получить маркер хранилища для операций серверной части уровня хранилища. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>Support Request Contributor (Участник с правом создавать запросы на поддержку)

Позволяет создавать запросы в службу поддержки и управлять ими.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="tag-contributor"></a>Участник тега

Позволяет управлять тегами в сущностях, не предоставляя доступ к самим сущностям.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft. Resources/Subscriptions/resourceGroups/Resources/Read | Возвращает ресурсы группы ресурсов. |
> | Microsoft.Resources/subscriptions/resources/read | Возвращает ресурсы подписки. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | Microsoft. Resources/Tags/* |  |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>Другой


### <a name="biztalk-contributor"></a>Участник BizTalk

Позволяет управлять службами BizTalk, но не доступом к ним.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.BizTalkServices/BizTalk/* | Создание служб BizTalk и управление ими |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Участник коллекции заданий планировщика

Позволяет управлять коллекциями заданий планировщика, но не доступом к ним.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание и управление классическим оповещением метрики |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертывания и управление им |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Scheduler/jobcollections/* | Создание коллекциями заданий и управление ими |
> | Microsoft.Support/* | Создание и обновление запроса в службу поддержки |
> | **NotActions** |  |
> | *отсутствуют* |  |
> | **Действия с данными** |  |
> | *отсутствуют* |  |
> | **NotDataActions** |  |
> | *отсутствуют* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>Дальнейшие действия

- [Сопоставить поставщик ресурсов со службой](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Пользовательские роли Azure](custom-roles.md)
- [Разрешения в центре безопасности Azure](../security-center/security-center-permissions.md)
