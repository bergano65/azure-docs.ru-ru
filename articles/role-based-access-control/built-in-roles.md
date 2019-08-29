---
title: Встроенные роли для ресурсов Azure | Документация Майкрософт
description: В этой статье описаны встроенные роли для управления доступом на основе ролей (RBAC) и ресурсов Azure. Выводит список элементов Actions, NotActions, DataActions и NotDataActions.
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
ms.date: 08/27/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: fb1007929a26384da60e542865c750fd1d642440
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114669"
---
# <a name="built-in-roles-for-azure-resources"></a>Встроенные роли для ресурсов Azure

[Управление доступом на основе ролей (RBAC)](overview.md) имеет несколько встроенных ролей, которые можно назначить для пользователей, групп, субъектов-служб и управляемых удостоверений. Назначение ролей является способом управления доступом к ресурсам Azure. Если встроенные роли не соответствуют потребностям вашей организации, вы можете создать собственные [настраиваемые роли для ресурсов Azure](custom-roles.md).

В этой статье перечислены встроенные роли для ресурсов Azure, которые всегда развиваются. Чтобы получить последние роли, используйте команду [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) или [az role definition list](/cli/azure/role/definition#az-role-definition-list). Если вам нужен список ролей администратора Azure Active Directory, см. статью [Разрешения роли администратора в Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="built-in-role-descriptions"></a>Описание встроенных ролей

В таблице ниже содержится краткое описание каждой из встроенных ролей. Щелкните имя роли, чтобы просмотреть список `Actions`, `NotActions`, `DataActions` и `NotDataActions` для каждой роли. Сведения о значении этих действий и как они применяются в области управления и области данных см. в статье [Общие сведения об определениях ролей для ресурсов Azure](role-definitions.md).


| Встроенные роли | Описание |
| --- | --- |
| [Владелец](#owner) | Позволяет управлять всем, в том числе доступом к ресурсам. |
| [Участник](#contributor) | Позволяет управлять всем, кроме доступа к ресурсам. |
| [Читатель](#reader) | Может просматривать все элементы, но не вносить изменения. |
| [акрделете](#acrdelete) | Удаление записи контроля доступа |
| [AcrImageSigner](#acrimagesigner) | Средство подписывания образов ACR |
| [AcrPull](#acrpull) | Извлечение ACR. |
| [AcrPush](#acrpush) | Отправка ACR. |
| [AcrQuarantineReader](#acrquarantinereader) | Модуль чтения данных карантина ACR. |
| [AcrQuarantineWriter](#acrquarantinewriter) | Модуль записи данных карантина ACR |
| [Участник службы управления API](#api-management-service-contributor) | Может управлять службой и интерфейсами API. |
| [Роль оператора службы управления API](#api-management-service-operator-role) | Может управлять службой, но не интерфейсами API. |
| [Роль читателя данных службы управления API](#api-management-service-reader-role) | Доступ к службе и интерфейсам API в режиме "только для чтения". |
| [Участник компонента Application Insights](#application-insights-component-contributor) | Может управлять компонентами Application Insights. |
| [Отладчик моментальных снимков Application Insights](#application-insights-snapshot-debugger) | Пользователю предоставляется разрешение на просмотр и загрузку моментальных снимков отладки, собранных с помощью Application Insights Snapshot Debugger. Обратите внимание, что эти разрешения не включены в роли [Владелец](#owner) или [Участник](#contributor). |
| [Оператор задания автоматизации](#automation-job-operator) | Создание заданий и управление ими с помощью модулей Runbook службы автоматизации. |
| [Оператор службы автоматизации](#automation-operator) | Операторы автоматизации могут запускать, останавливать, приостанавливать и возобновлять задания. |
| [Оператор Runbook автоматизации](#automation-runbook-operator) | Чтение свойств Runbook, позволяющее создавать задания Runbook. |
| [Участник авере](#avere-contributor) | Может создавать кластер Авере Вфкст и управлять им. |
| [Оператор авере](#avere-operator) | Используется кластером Авере Вфкст для управления кластером. |
| [Владелец данных концентраторов событий Azure](#azure-event-hubs-data-owner) | Обеспечивает полный доступ к ресурсам концентраторов событий Azure. |
| [Приемник данных концентраторов событий Azure](#azure-event-hubs-data-receiver) | Разрешает получать доступ к ресурсам концентраторов событий Azure. |
| [Отправитель данных концентраторов событий Azure](#azure-event-hubs-data-sender) | Разрешает отправку доступа к ресурсам концентраторов событий Azure. |
| [Роль администратора кластера в Службе Azure Kubernetes](#azure-kubernetes-service-cluster-admin-role) | Список действий, выполненных с помощью учетных данных администратора кластера. |
| [Роль пользователя кластера в Службе Azure Kubernetes](#azure-kubernetes-service-cluster-user-role) | Список действий, выполненных с помощью учетных данных пользователя кластера. |
| [Azure Maps модуль чтения данных (Предварительная версия)](#azure-maps-data-reader-preview) | Предоставляет доступ для чтения данных, связанных с картой, из учетной записи Azure Maps. |
| [Владелец данных служебной шины Azure](#azure-service-bus-data-owner) | Обеспечивает полный доступ к ресурсам служебной шины Azure. |
| [Приемник данных служебной шины Azure](#azure-service-bus-data-receiver) | Разрешает получать доступ к ресурсам служебной шины Azure. |
| [Отправитель данных служебной шины Azure](#azure-service-bus-data-sender) | Разрешает отправку доступа к ресурсам служебной шины Azure. |
| [Владелец регистрации Azure Stack](#azure-stack-registration-owner) | Позволяет управлять регистрациями Azure Stack. |
| [Участник резервного копирования](#backup-contributor) | Позволяет управлять службой архивации, но не может создавать хранилища и предоставлять доступ другим пользователям. |
| [Оператор резервного копирования](#backup-operator) | Позволяет управлять службами архивации, но не удалять архивные копии, создавать хранилища или предоставлять доступ другим пользователям |
| [Читатель резервных копий](#backup-reader) | Может просматривать службы резервного копирования, но не может вносить изменения. |
| [Читатель счетов](#billing-reader) | Разрешает читать данные выставления счетов. |
| [Участник BizTalk](#biztalk-contributor) | Позволяет управлять службами BizTalk, но не доступом к ним. |
| [Доступ к узлу-члену блокчейн (Предварительная версия)](#blockchain-member-node-access-preview) | Разрешает доступ к узлам члена Блокчейн |
| [Участник схемы](#blueprint-contributor) | Может управлять определениями схем, но не назначать их. |
| [Оператор схемы](#blueprint-operator) | Может назначать существующие опубликованные проекты, но не может создавать новые. Примечание. это работает только в том случае, если назначение выполняется с управляемым удостоверением, назначенным пользователем. |
| [Участник конечных точек CDN](#cdn-endpoint-contributor) | Может управлять конечными точками CDN, но не может предоставлять доступ другим пользователям. |
| [Читатель конечной точки CDN](#cdn-endpoint-reader) | Может просматривать конечные точки CDN, но не может вносить изменения. |
| [Участник профиля CDN](#cdn-profile-contributor) | Может управлять профилями CDN и их конечными точками, но не может предоставлять доступ другим пользователям. |
| [Читатель данных профиля CDN](#cdn-profile-reader) | Может просматривать профили CDN и их конечные точки, но не может вносить изменения. |
| [Участник классической сети](#classic-network-contributor) | Позволяет управлять классическими сетями, но не доступом к ним. |
| [Участник классической учетной записи хранения](#classic-storage-account-contributor) | Позволяет управлять классическими учетными записями хранения, но не предоставлять доступ к ним. |
| [Роль службы оператора ключей классических учетных записей хранения](#classic-storage-account-key-operator-service-role) | Операторы ключей классических учетных записей хранения могут перечислять и повторно создавать ключи в классических учетных записях хранения. |
| [Участник классической виртуальной машины](#classic-virtual-machine-contributor) | Позволяет управлять классическими виртуальными машинами, но не доступом к ним и не учетной записью виртуальной сети или хранения, к которой они подключены. |
| [Участник служб Cognitive Services](#cognitive-services-contributor) | Позволяет создавать, читать, обновлять, удалять ключи служб Cognitive Services и управлять ими. |
| [Cognitive Services модуль чтения данных (Предварительная версия)](#cognitive-services-data-reader-preview) | Позволяет считывать данные Cognitive Services. |
| [Пользователь служб Cognitive Services](#cognitive-services-user) | Позволяет создавать и читать список ключей служб Cognitive Services. |
| [Роль читателя учетных записей Cosmos DB](#cosmos-db-account-reader-role) | Позволяет считывать данные учетных записей Azure Cosmos DB. Сведения об управлении учетными записями Azure Cosmos DB см. в разделе [Участник учетной записи DocumentDB](#documentdb-account-contributor). |
| [Оператор Cosmos DB](#cosmos-db-operator) | Позволяет управлять учетными записями Azure Cosmos DB, но не доступом к данным. Предотвращает доступ к ключам учетной записи и строкам подключения. |
| [CosmosBackupOperator](#cosmosbackupoperator) | Позволяет отправлять запрос на восстановление контейнера или базы данных Cosmos DB для учетной записи. |
| [Участник службы "Управление затратами"](#cost-management-contributor) | Позволяет просматривать расходы и управлять конфигурацией затрат (например, бюджеты, экспорты) |
| [Читатель службы "Управление затратами"](#cost-management-reader) | Позволяет просматривать данные о расходах и конфигурации (например, бюджеты, экспорты) |
| [Участник Data Box](#data-box-contributor) | Позволяет управлять всеми данными службы Data Box, кроме предоставления доступа другим пользователям. |
| [Читатель Data Box](#data-box-reader) | Позволяет управлять службой Data Box, но не позволяет создавать заказы и менять их порядок, а также предоставлять доступ другим пользователям. |
| [Участник фабрики данных](#data-factory-contributor) | Создание фабрик данных и управление ими, а также их дочерними ресурсами. |
| [Разработчик Data Lake Analytics](#data-lake-analytics-developer) | Позволяет отправлять, отслеживать задания и управлять ими, но не позволяет создавать или удалять учетные записи Data Lake Analytics. |
| [Средство очистки данных](#data-purger) | Может очистить данные аналитики |
| [Пользователь DevTest Labs](#devtest-labs-user) | Позволяет подключать, запускать, перезапускать виртуальные машины и завершать их работу в Azure DevTest Labs. |
| [Участник зоны DNS](#dns-zone-contributor) | Позволяет управлять зонами DNS и наборами записей в Azure DNS, но не тем, кому они будут доступны. |
| [Участник учетной записи DocumentDB](#documentdb-account-contributor) | Может управлять учетными записями Azure Cosmos DB Служба Azure Cosmos DB раньше называлась DocumentDB. |
| [Участник EventGrid EventSubscription](#eventgrid-eventsubscription-contributor) | Позволяет управлять операциями с подписками на события Сетки событий. |
| [Читатель EventGrid EventSubscription](#eventgrid-eventsubscription-reader) | Позволяет получить доступ на чтение к подпискам на события Сетки событий. |
| [Оператор кластера HDInsight](#hdinsight-cluster-operator) | Позволяет читать и изменять конфигурации кластера HDInsight. |
| [Участник доменных служб HDInsight](#hdinsight-domain-services-contributor) | Позволяет читать, создавать, изменять и удалять операции, связанные с доменными службами, необходимыми для Корпоративного пакета безопасности HDInsight |
| [Участник учетной записи интеллектуальных систем](#intelligent-systems-account-contributor) | Позволяет управлять учетными записями интеллектуальных систем, но не доступом к ним. |
| [Участник Key Vault](#key-vault-contributor) | Позволяет управлять хранилищами ключей, но не доступом к ним. |
| [Создатель лаборатории](#lab-creator) | Позволяет создавать, администрировать и удалять управляемые лаборатории в учетных записях Служб лабораторий Azure. |
| [Участник Log Analytics](#log-analytics-contributor) | Участник Log Analytics может считывать все данные мониторинга и изменять параметры мониторинга. Изменение параметров мониторинга подразумевает добавление расширений в виртуальные машины, чтение ключей учетной записи хранения для настройки коллекции журналов в службе хранилища Azure, создание и настройку учетных записей службы автоматизации, добавление решений и настройку диагностики Azure во всех ресурсах Azure. |
| [Читатель Log Analytics](#log-analytics-reader) | Читатель Log Analytics может просматривать все данные мониторинга, выполнять по ним поиск и просматривать параметры мониторинга, в том числе конфигурацию системы диагностики Azure для всех ресурсов Azure. |
| [Создатель приложений логики](#logic-app-contributor) | Позволяет управлять приложениями логики, но не изменяет доступ к ним. |
| [Оператор приложений логики](#logic-app-operator) | Позволяет читать, включать и отключать приложения логики, но не изменять и не обновлять их. |
| [Роль оператора управляемого приложения](#managed-application-operator-role) | Позволяет читать и выполнять действия с ресурсами управляемого приложения. |
| [Читатель Управляемых приложений](#managed-applications-reader) | Позволяет считывать ресурсы в управляемом приложении и запрашивать доступ JIT. |
| [Участник управляемого удостоверения](#managed-identity-contributor) | Создание, чтение, обновление и удаление пользовательских удостоверений. |
| [Оператор управляемого удостоверения](#managed-identity-operator) | Чтение и назначение пользовательских удостоверений. |
| [Участник группы управления](#management-group-contributor) | Роль участника группы управления |
| [Читатель группы управления](#management-group-reader) | Роль читателя группы управления |
| [Monitoring Contributor](#monitoring-contributor) (Участник мониторинга) | Может читать все данные мониторинга и изменять параметры мониторинга. Ознакомьтесь также со статьей [Приступая к работе с ролями, разрешениями и системой безопасности с помощью Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Издатель метрик мониторинга](#monitoring-metrics-publisher) | Включает публикацию метрик ресурсов Azure |
| [Monitoring Reader](#monitoring-reader) (Читатель данных мониторинга) | Может читать все данные мониторинга (метрики, журналы и т. д.). Ознакомьтесь также со статьей [Приступая к работе с ролями, разрешениями и системой безопасности с помощью Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Участник сети](#network-contributor) | Позволяет управлять сетями, но не доступом к ним. |
| [Участник учетной записи New Relic APM](#new-relic-apm-account-contributor) | Позволяет управлять учетными записями и приложениями New Relic Application Performance Management, но не доступом к ним. |
| [Модуль чтения и доступ к данным](#reader-and-data-access) | Позволяет просматривать все данные, но не позволит удалить или создать учетную запись хранения или содержащий ресурс. Он также предоставит доступ на чтение и запись для всех данных, содержащихся в учетной записи хранения через доступ к ключам учетной записи хранения. |
| [Участник кэша Redis](#redis-cache-contributor) | Позволяет управлять кэшем Redis, но не доступом к нему. |
| [Участник политики ресурсов (предварительная версия)](#resource-policy-contributor-preview) | Обратное заполнение пользователей из EA с правами на создание и изменение политики ресурсов, создание запросов в службу поддержки и чтение ресурсов и иерархии (предварительная версия). |
| [Участник коллекции заданий планировщика](#scheduler-job-collections-contributor) | Позволяет управлять коллекциями заданий планировщика, но не доступом к ним. |
| [Участник службы поиска](#search-service-contributor) | Позволяет управлять службами поиска, но не доступом к ним. |
| [Администратор безопасности](#security-admin) | Только в Центре безопасности. Может просматривать политики и состояния безопасности, изменять политики безопасности, просматривать оповещения и рекомендации, а также закрывать предупреждения и рекомендации. |
| [Диспетчер безопасности (устаревший)](#security-manager-legacy) | Это устаревшая роль. Используйте роль администратора безопасности |
| [Читатель сведений о безопасности](#security-reader) | Только в Центре безопасности. Может просматривать оповещения и рекомендации, просматривать политики и состояния безопасности, но не может вносить изменения. |
| [Участник Site Recovery](#site-recovery-contributor) | Позволяет управлять службой Site Recovery, за исключением создания хранилищ и назначения ролей. |
| [Оператор Site Recovery](#site-recovery-operator) | Позволяет выполнять отработку отказа и восстановление размещения, но не другие операции управления Site Recovery. |
| [Читатель Site Recovery](#site-recovery-reader) | Позволяет просматривать состояние Site Recovery без выполнения других операций управления. |
| [Участник учетной записи пространственных привязок](#spatial-anchors-account-contributor) | Позволяет управлять пространственными привязками в учетной записи, но не удалять их |
| [Владелец учетной записи пространственных привязок](#spatial-anchors-account-owner) | Позволяет управлять пространственными привязками в учетной записи, включая их удаление. |
| [Читатель учетной записи пространственных привязок](#spatial-anchors-account-reader) | Позволяет нахождение и чтение свойств пространственных привязок в учетной записи. |
| [Участник базы данных SQL](#sql-db-contributor) | Позволяет управлять базами данных SQL, но не доступом к ним. Кроме того, не позволяет управлять их политиками безопасности или родительскими серверами SQL Server. |
| [Участник SQL Управляемый экземпляр](#sql-managed-instance-contributor) | Позволяет управлять управляемыми экземплярами SQL и требуемой конфигурацией сети, но не может предоставлять доступ другим пользователям. |
| [Диспетчер безопасности SQL](#sql-security-manager) | Позволяет управлять политиками безопасности серверов SQL Server и баз данных SQL, но не доступом к ним. |
| [Участник SQL Server](#sql-server-contributor) | Позволяет управлять серверами SQL Server и базами данных SQL, но не доступом к ним и их политиками безопасности. |
| [Участник учетной записи хранения](#storage-account-contributor) | Разрешает управление учетными записями хранения. Предоставляет доступ к ключу учетной записи, который можно использовать для доступа к данным через авторизацию с общим ключом. |
| [Роль службы оператора ключей учетных записей хранения](#storage-account-key-operator-service-role) | Разрешает перечисление и повторное создание ключей доступа к учетной записи хранения. |
| [участник данных BLOB-объектов хранилища](#storage-blob-data-contributor); | Чтение, запись и удаление контейнеров службы хранилища Azure и больших двоичных объектов. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [владелец данных BLOB-объектов хранилища](#storage-blob-data-owner); | Предоставляет полный доступ к контейнерам и данным больших двоичных объектов службы хранилища Azure, включая назначение управления доступом POSIX. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [читатель данных больших двоичных объектов хранилища](#storage-blob-data-reader). | Прочтите и перечислите контейнеры и большие двоичные объекты службы хранилища Azure. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Делегирование BLOB-объектов хранилища](#storage-blob-delegator) | Получите ключ делегирования пользователя, который затем можно использовать для создания подписи общего доступа для контейнера или большого двоичного объекта, подписанного с помощью учетных данных Azure AD. Дополнительные сведения см. [в разделе Создание SAS для делегирования пользователей](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). |
| [Участник общей папки SMB данных файлов хранилища](#storage-file-data-smb-share-contributor) | Разрешает доступ на чтение, запись и удаление в общих файловых ресурсах службы хранилища Azure через SMB. |
| [Участник хранилища данных файлового ресурса SMB с повышенными правами](#storage-file-data-smb-share-elevated-contributor) | Разрешение на чтение, запись, удаление и изменение разрешений NTFS в общих файловых ресурсах службы хранилища Azure через SMB. |
| [Средство чтения общего ресурса SMB с данными файлов хранилища](#storage-file-data-smb-share-reader) | Разрешает доступ на чтение к файловому ресурсу Azure через SMB. |
| [Участник данных очереди хранилища](#storage-queue-data-contributor) | Чтение, запись и удаление очередей службы хранилища Azure и сообщений очереди. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Обработчик сообщений данных очереди хранилища](#storage-queue-data-message-processor) | Просмотр, извлечение и удаление сообщения из очереди службы хранилища Azure. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Отправитель сообщений очереди хранилища](#storage-queue-data-message-sender) | Добавление сообщений в очередь службы хранилища Azure. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Модуль чтения данных очереди хранилища](#storage-queue-data-reader) | Чтение и вывод списка очередей службы хранилища Azure и сообщений очереди. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Support Request Contributor](#support-request-contributor) (Участник с правом создавать запросы на поддержку) | Позволяет создавать запросы в службу поддержки и управлять ими. |
| [Участник диспетчера трафика](#traffic-manager-contributor) | Позволяет управлять профилями диспетчера трафика, но не доступом к ним. |
| [Администратор доступа пользователей](#user-access-administrator) | Позволяет управлять доступом пользователей к ресурсам Azure. |
| [Администратор виртуальной машины](#virtual-machine-administrator-login) | Позволяет просматривать виртуальные машины на портале и входить в систему с правами администратора. |
| [Участник виртуальной машины](#virtual-machine-contributor) | Позволяет управлять виртуальными машинами, но не доступом к ним и не виртуальной сетью или учетной записью хранения, к которой они подключены. |
| [Пользователь виртуальной машины](#virtual-machine-user-login) | Позволяет просматривать виртуальные машины на портале и входить в систему с правами обычного пользователя. |
| [Участник веб-плана](#web-plan-contributor) | Позволяет управлять веб-планами для веб-сайтов, но не доступом к ним. |
| [Участник веб-сайта](#website-contributor) | Позволяет управлять веб-сайтами (не веб-планами), но не доступом к ним. |


## <a name="owner"></a>Ответственный
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять всем, в том числе доступом к ресурсам. |
> | **Id** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Действия** |  |
> | * | Создание ресурсов всех типов и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="contributor"></a>Участник
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять всем, кроме доступа к ресурсам. |
> | **Id** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Действия** |  |
> | * | Создание ресурсов всех типов и управление ими |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | Удаление и назначение ролей. |
> | Microsoft.Authorization/*/Write | Создание и назначение ролей. |
> | Microsoft.Authorization/elevateAccess/Action | Предоставляет вызывающему доступ с правами администратора для области действия клиента. |
> | Microsoft.Blueprint/blueprintAssignments/write | Создание или изменение любых артефактов схемы |
> | Microsoft.Blueprint/blueprintAssignments/delete | Удаление любых артефактов схемы. |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="reader"></a>Читатель
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может просматривать все элементы, но не вносить изменения. |
> | **Id** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="acrdelete"></a>акрделете
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Удаление записи контроля доступа |
> | **Id** | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | **Действия** |  |
> | Microsoft. ContainerRegistry/реестры/артефакты/удаление | Удаление артефакта в реестре контейнеров. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Средство подписывания образов ACR |
> | **Id** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Действия** |  |
> | Microsoft.ContainerRegistry/registries/sign/write | Передача или извлечение метаданных доверия к содержимому для реестра контейнеров. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="acrpull"></a>AcrPull
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Извлечение ACR. |
> | **Id** | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | **Действия** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Извлечение или получение образов из реестра контейнеров. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="acrpush"></a>AcrPush
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Отправка ACR. |
> | **Id** | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | **Действия** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Извлечение или получение образов из реестра контейнеров. |
> | Microsoft.ContainerRegistry/registries/push/write | Передача или запись образов в реестр контейнеров. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Модуль чтения данных карантина ACR |
> | **Id** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Действия** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | Извлечение или получение помещенных в карантин образов из реестра контейнеров. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Модуль записи данных карантина ACR |
> | **Id** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Действия** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | Извлечение или получение помещенных в карантин образов из реестра контейнеров. |
> | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Запись и изменение состояния карантина образов, помещенных в карантин. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="api-management-service-contributor"></a>Участник службы управления API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может управлять службой и интерфейсами API. |
> | **Id** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Действия** |  |
> | Microsoft.ApiManagement/service/* | Создание службы управления API и управление ею |
> | Microsoft.Authorization/*/read | Авторизация на чтение |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="api-management-service-operator-role"></a>Роль оператора службы управления API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может управлять службой, но не интерфейсами API. |
> | **Id** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Действия** |  |
> | Microsoft.ApiManagement/service/*/read | Чтение экземпляров службы управления API. |
> | Microsoft.ApiManagement/service/backup/action | Архивирует службу управления API в указанный контейнер в предоставленной пользователем учетной записи хранения. |
> | Microsoft.ApiManagement/service/delete | Удаляет экземпляр службы управления API. |
> | Microsoft.ApiManagement/service/managedeployments/action | Позволяет изменить номер SKU или единицы, а также добавить или удалить региональные развертывания службы управления API. |
> | Microsoft.ApiManagement/service/read | Чтение метаданных для экземпляра службы управления API. |
> | Microsoft.ApiManagement/service/restore/action | Восстановление службы управления API из указанного контейнера в предоставленной пользователем учетной записи хранения. |
> | Microsoft.ApiManagement/service/updatecertificate/action | Передает SSL-сертификата для службы управления API. |
> | Microsoft.ApiManagement/service/updatehostname/action | Позволяет настроить, обновить или удалить имена личных доменов для службы управления API. |
> | Microsoft.ApiManagement/service/write | Создание экземпляра службы управления API. |
> | Microsoft.Authorization/*/read | Авторизация на чтение |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Получение ключей, связанных с пользователем |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="api-management-service-reader-role"></a>Роль читателя данных службы управления API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Доступ к службе и интерфейсам API в режиме "только для чтения". |
> | **Id** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Действия** |  |
> | Microsoft.ApiManagement/service/*/read | Чтение экземпляров службы управления API. |
> | Microsoft.ApiManagement/service/read | Чтение метаданных для экземпляра службы управления API. |
> | Microsoft.Authorization/*/read | Авторизация на чтение |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Получение ключей, связанных с пользователем |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="application-insights-component-contributor"></a>Участник компонентов Application Insights
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может управлять компонентами Application Insights. |
> | **Id** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.Insights/components/* | Создание компонентов Insights и управление ими |
> | Microsoft.Insights/webtests/* | Создание веб-тестов и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Пользователю предоставляется разрешение на просмотр и загрузку моментальных снимков отладки, собранных с помощью Application Insights Snapshot Debugger. Обратите внимание, что эти разрешения не включены в роли [Владелец](#owner) или [Участник](#contributor). |
> | **Id** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="automation-job-operator"></a>Оператор задания автоматизации
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Создание заданий и управление ими с помощью модулей Runbook службы автоматизации. |
> | **Id** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
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
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="automation-operator"></a>Оператор автоматизации
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Операторы автоматизации могут запускать, останавливать, приостанавливать и возобновлять задания. |
> | **Id** | d3881f73-407a-4167-8283-e981cbba0404 |
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
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Возвращает выходные данные задания. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="automation-runbook-operator"></a>Оператор Runbook автоматизации
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Чтение свойств Runbook, позволяющее создавать задания Runbook. |
> | **Id** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Automation/automationAccounts/runbooks/read | Возвращает runbook службы автоматизации Azure. |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="avere-contributor"></a>Участник авере
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может создавать кластер Авере Вфкст и управлять им. |
> | **Id** | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
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
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft. Storage/*/Реад |  |
> | Microsoft.Storage/storageAccounts/* |  |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | Microsoft. Resources/Subscriptions/resourceGroups/Resources/Read | Возвращает ресурсы группы ресурсов. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Возвращение результата, полученного при удалении большого двоичного объекта. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Возвращение большого двоичного объекта или списка таких объектов. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Возвращение результата, полученного при записи большого двоичного объекта. |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="avere-operator"></a>Оператор авере
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Используется кластером Авере Вфкст для управления кластером. |
> | **Id** | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
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
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Возвращение результата, полученного при удалении большого двоичного объекта. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Возвращение большого двоичного объекта или списка таких объектов. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Возвращение результата, полученного при записи большого двоичного объекта. |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="azure-event-hubs-data-owner"></a>Владелец данных концентраторов событий Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Обеспечивает полный доступ к ресурсам концентраторов событий Azure. |
> | **Id** | f526a384-b230-433a-b45c-95f59c4a2dec |
> | **Действия** |  |
> | Microsoft. EventHub/* |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft. EventHub/* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="azure-event-hubs-data-receiver"></a>Приемник данных концентраторов событий Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Разрешает получать доступ к ресурсам концентраторов событий Azure. |
> | **Id** | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | **Действия** |  |
> | Microsoft.EventHub/*/eventhubs/consumergroups/read |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft. EventHub/*/рецеиве/Актион |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="azure-event-hubs-data-sender"></a>Отправитель данных концентраторов событий Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Разрешает отправку доступа к ресурсам концентраторов событий Azure. |
> | **Id** | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | **Действия** |  |
> | Microsoft.EventHub/*/eventhubs/read |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft. EventHub/*/Сенд/Актион |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Роль администратора кластера в Службе Azure Kubernetes
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Список действий, выполненных с помощью учетных данных администратора кластера. |
> | **Id** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Действия** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Перечисляет учетные данные clusterAdmin управляемого кластера. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="azure-kubernetes-service-cluster-user-role"></a>Роль пользователя кластера в Службе Azure Kubernetes
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Список действий, выполненных с помощью учетных данных пользователя кластера. |
> | **Id** | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Действия** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Перечисляет учетные данные clusterUser управляемого кластера. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="azure-maps-data-reader-preview"></a>Читатель данных Azure Maps (предварительная версия)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Предоставляет доступ для чтения данных, связанных с картой, из учетной записи Azure Maps. |
> | **Id** | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | **Действия** |  |
> | *Нет* |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft.Maps/accounts/data/read | Предоставляет доступ на чтение данных учетных записей карт. |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="azure-service-bus-data-owner"></a>Владелец данных служебной шины Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Обеспечивает полный доступ к ресурсам служебной шины Azure. |
> | **Id** | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | **Действия** |  |
> | Microsoft. ServiceBus/* |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft. ServiceBus/* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="azure-service-bus-data-receiver"></a>Приемник данных служебной шины Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Разрешает получать доступ к ресурсам служебной шины Azure. |
> | **Id** | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | **Действия** |  |
> | Microsoft. ServiceBus/*/куеуес/Реад |  |
> | Microsoft. ServiceBus/*/топикс/Реад |  |
> | Microsoft. ServiceBus/*/топикс/субскриптионс/Реад |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft. ServiceBus/*/рецеиве/Актион |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="azure-service-bus-data-sender"></a>Отправитель данных служебной шины Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Разрешает отправку доступа к ресурсам служебной шины Azure. |
> | **Id** | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | **Действия** |  |
> | Microsoft. ServiceBus/*/куеуес/Реад |  |
> | Microsoft. ServiceBus/*/топикс/Реад |  |
> | Microsoft. ServiceBus/*/топикс/субскриптионс/Реад |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft. ServiceBus/*/Сенд/Актион |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="azure-stack-registration-owner"></a>Владелец регистрации Azure Stack
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять регистрациями Azure Stack. |
> | **Id** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Действия** |  |
> | Microsoft. AzureStack/регистрации/Products/*/Action |  |
> | Microsoft.AzureStack/registrations/products/read | Получает свойства продукта из Azure Stack Marketplace. |
> | Microsoft.AzureStack/registrations/read | Получает свойства регистрации Azure Stack. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="backup-contributor"></a>Участник архивации
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять службой архивации, но не может создавать хранилища и предоставлять доступ другим пользователям. |
> | **Id** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Управление результатами операций управления резервным копированием |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Создание контейнеров резервных копий внутри структуры резервного копирования хранилища служб восстановления и управление этими контейнерами |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Обновляет список контейнеров. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Создание заданий резервного копирования и управление ими |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Экспорт заданий |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Создание метаданных, связанных с управлением резервным копированием, и управление ими |
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
> | Microsoft.RecoveryServices/Vaults/read | Операция "Получить хранилище" получает объект, представляющий ресурс Azure типа "vault" |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Создание зарегистрированных удостоверений и управление ими |
> | Microsoft.RecoveryServices/Vaults/usages/* | Создание хранилища служб восстановления и управление его использованием |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Проверка операций для защищенного элемента |
> | Microsoft.RecoveryServices/Vaults/write | Операция "Создать хранилище" создает ресурс Azure типа vault. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Возвращает состояние операции архивации для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Возвращает все серверы управления архивацией, зарегистрированные в хранилище. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Получение всех защищаемых контейнеров. |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Проверка состояния архивации хранилищ служб восстановления. |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Проверка компонентов. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Разрешение оповещения. |
> | Microsoft.RecoveryServices/operations/read | Получение списка операций для поставщика ресурсов. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Возвращает состояние операции для данной операции |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Вывод списка всех целей защиты для резервного копирования. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="backup-operator"></a>Оператор архивации
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять службами архивации, но не удалять архивные копии, создавать хранилища или предоставлять доступ другим пользователям |
> | **Id** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Возвращает состояние операции |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Получение результата операции с контейнером защиты. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Выполнение архивации для защищенного элемента. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Получение результата операций с защищенными элементами. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Возврат состояния для операции с защищенными элементами. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Возвращает сведения об объекте защищенного элемента |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Подготавливает мгновенное восстановление для защищенного элемента. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Получение точек восстановления для защищенных элементов. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Восстановление точек восстановления для защищенных элементов. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Отменяет мгновенное восстановление для защищенного элемента. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Создает элемент, защищенный службой архивации. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Возвращает все зарегистрированные контейнеры. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Обновляет список контейнеров. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Создание заданий резервного копирования и управление ими |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Экспорт заданий |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Создание результатов операций управления резервным копированием и управление ими |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Получение результатов работы политики. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Возвращает все политики защиты. |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Создание элементов, для которых можно создавать резервные копии, и управление ими |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Возврат списка всех защищенных элементов. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Возвращает все контейнеры, принадлежащие подписке |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Возвращает сводки по защищенным элементам и защищенным серверам для служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Операция "Обновить сертификат ресурсов" обновляет сертификат учетных данных ресурсов или хранилища. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Операция "Получить расширенные сведения" получает расширенные сведения об объекте, представляющие ресурс Azure типа ?vault? |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Операция "Получить расширенные сведения" получает расширенные сведения об объекте, представляющие ресурс Azure типа ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Возвращает оповещения для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Операция "Получить хранилище" получает объект, представляющий ресурс Azure типа "vault" |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Операцию "Получить результаты операции" можно использовать для получения состояния операции и результата асинхронно отправленной операции |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Операцию "Получить контейнеры" можно использовать для регистрации контейнеров в ресурсе. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Операцию "Зарегистрировать контейнер службы" можно использовать для регистрации контейнера в службе восстановления. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Возвращает подробности об использовании для хранилища служб восстановления. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Проверка операций для защищенного элемента |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Возвращает состояние операции архивации для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Возвращает состояние операции политики. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Создание зарегистрированного контейнера. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Запрос рабочих нагрузок в контейнере. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Возвращает все серверы управления архивацией, зарегистрированные в хранилище. |
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
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="backup-reader"></a>Модуль чтения архивных копий
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может просматривать службы резервного копирования, но не может вносить изменения. |
> | **Id** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp — внутренняя операция, используемая службой |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Возвращает состояние операции |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Получение результата операции с контейнером защиты. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Получение результата операций с защищенными элементами. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Возврат состояния для операции с защищенными элементами. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Возвращает сведения об объекте защищенного элемента |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Получение точек восстановления для защищенных элементов. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Возвращает все зарегистрированные контейнеры. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Возврат результата для операции задания. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Возвращает все объекты заданий. |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Экспорт заданий |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Возврат результата архивации для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Получение результатов работы политики. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Возвращает все политики защиты. |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Возврат списка всех защищенных элементов. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Возвращает все контейнеры, принадлежащие подписке |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Возвращает сводки по защищенным элементам и защищенным серверам для служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Операция "Получить расширенные сведения" получает расширенные сведения об объекте, представляющие ресурс Azure типа ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Возвращает оповещения для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/read | Операция "Получить хранилище" получает объект, представляющий ресурс Azure типа "vault" |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Операцию "Получить результаты операции" можно использовать для получения состояния операции и результата асинхронно отправленной операции |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Операцию "Получить контейнеры" можно использовать для регистрации контейнеров в ресурсе. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Возвращает конфигурацию службы хранилища для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Возвращает конфигурацию хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Возвращает состояние операции архивации для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Возвращает состояние операции политики. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Возвращает все серверы управления архивацией, зарегистрированные в хранилище. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Получение цели защиты для резервного копирования. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Получение всех элементов в контейнере. |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Проверка состояния архивации хранилищ служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Разрешение оповещения. |
> | Microsoft.RecoveryServices/operations/read | Получение списка операций для поставщика ресурсов. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Возвращает состояние операции для данной операции |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Вывод списка всех целей защиты для резервного копирования. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Возвращает подробности об использовании для хранилища служб восстановления. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="billing-reader"></a>Средство чтения счетов
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Разрешает читать данные выставления счетов. |
> | **Id** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Billing/*/read | Чтение сведений о выставлении счетов |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | Вывод списка групп управления для пользователя, прошедшего проверку подлинности. |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="biztalk-contributor"></a>Участник BizTalk
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять службами BizTalk, но не доступом к ним. |
> | **Id** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.BizTalkServices/BizTalk/* | Создание служб BizTalk и управление ими |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="blockchain-member-node-access-preview"></a>Доступ к узлу-члену блокчейн (Предварительная версия)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Разрешает доступ к узлам члена Блокчейн |
> | **Id** | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **Действия** |  |
> | Microsoft. Блокчейн/Блоккчаинмемберс/Трансактионнодес/Read | Возвращает или перечисляет существующие узлы транзакций с элементами Блокчейн. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft. Блокчейн/Блоккчаинмемберс/Трансактионнодес/Connect/Action | Подключается к узлу транзакции-члена Блокчейн. |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="blueprint-contributor"></a>Участник схемы
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может управлять определениями схем, но не назначать их. |
> | **Id** | 41077137-E803-4205-871c-5a86e6a753b4 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft. чертеж/чертежи/* | Создание определений схем или артефактов схемы и управление ими. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="blueprint-operator"></a>Оператор схемы
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может назначать существующие опубликованные проекты, но не может создавать новые. Примечание. это работает только в том случае, если назначение выполняется с управляемым удостоверением, назначенным пользователем. |
> | **Id** | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft. чертеж/Блуепринтассигнментс/* | Создание назначений схем и управление ими. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="cdn-endpoint-contributor"></a>Участник конечных точек CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может управлять конечными точками CDN, но не может предоставлять доступ другим пользователям. |
> | **Id** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="cdn-endpoint-reader"></a>Читатель конечной точки CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может просматривать конечные точки CDN, но не может вносить изменения. |
> | **Id** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="cdn-profile-contributor"></a>Участник профиля CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может управлять профилями CDN и их конечными точками, но не может предоставлять доступ другим пользователям. |
> | **Id** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="cdn-profile-reader"></a>Читатель данных профиля CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может просматривать профили CDN и их конечные точки, но не может вносить изменения. |
> | **Id** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="classic-network-contributor"></a>Участник классических сетей
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять классическими сетями, но не доступом к ним. |
> | **Id** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Авторизация на чтение |
> | Microsoft.ClassicNetwork/* | Создание классических сетей и управление ими |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="classic-storage-account-contributor"></a>Участник классических учетных записей хранения
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять классическими учетными записями хранения, но не предоставлять доступ к ним. |
> | **Id** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Авторизация на чтение |
> | Microsoft.ClassicStorage/storageAccounts/* | Создание учетных записей хранения и управление ими |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="classic-storage-account-key-operator-service-role"></a>Роль службы оператора ключей классических учетных записей хранения
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Операторы ключей классических учетных записей хранения могут перечислять и повторно создавать ключи в классических учетных записях хранения. |
> | **Id** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Действия** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Отображает ключи доступа для учетных записей хранения. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Повторно создает существующие ключи доступа для учетной записи хранения. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="classic-virtual-machine-contributor"></a>Участник классических виртуальных машин
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять классическими виртуальными машинами, но не доступом к ним и не учетной записью виртуальной сети или хранения, к которой они подключены. |
> | **Id** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Авторизация на чтение |
> | Microsoft.ClassicCompute/domainNames/* | Создание доменных имен для классических вычислений и управление ими |
> | Microsoft.ClassicCompute/virtualMachines/* | Создание виртуальных машин и управление ими |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Связь зарезервированного IP-адреса |
> | Microsoft.ClassicNetwork/reservedIps/read | Получает зарезервированные IP-адреса |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Подключает виртуальную сеть. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Возвращает виртуальную сеть. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | Возвращает диск в учетной записи хранения. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | Возвращает образ в учетной записи хранения. (Не рекомендуется к использованию. Следует использовать Microsoft.ClassicStorage/storageAccounts/vmImages.) |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Отображает ключи доступа для учетных записей хранения. |
> | Microsoft.ClassicStorage/storageAccounts/read | Возвращает учетную запись хранения для заданной учетной записи. |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="cognitive-services-contributor"></a>Участник Cognitive Services
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет создавать, читать, обновлять, удалять ключи служб Cognitive Services и управлять ими. |
> | **Id** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | Возвращает функции подписки. |
> | Microsoft.Features/providers/features/read | Возвращает функцию подписки в заданном поставщике ресурсов. |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Insights/diagnosticSettings/* | Создание, обновление или считывание параметра диагностики для сервера анализа данных. |
> | Microsoft.Insights/logDefinitions/read | Чтение определений в журнале |
> | Microsoft.Insights/metricdefinitions/read | Чтение определений метрик |
> | Microsoft.Insights/metrics/read | Метрик считано |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/deployments/operations/read | Возвращает операции развертывания или выводит их список. |
> | Microsoft.Resources/subscriptions/operationresults/read | Возвращает результаты операции подписки. |
> | Microsoft.Resources/subscriptions/read | Возвращает список подписок. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="cognitive-services-data-reader-preview"></a>Cognitive Services модуль чтения данных (Предварительная версия)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет считывать данные Cognitive Services. |
> | **Id** | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | **Действия** |  |
> | *Нет* |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="cognitive-services-user"></a>Пользователь Cognitive Services
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет создавать и читать список ключей служб Cognitive Services. |
> | **Id** | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Действия** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | Список ключей |
> | Microsoft.Insights/alertRules/read | Чтение классического оповещения метрики |
> | Microsoft.Insights/diagnosticSettings/read | Чтение параметра диагностики для ресурсов |
> | Microsoft.Insights/logDefinitions/read | Чтение определений в журнале |
> | Microsoft.Insights/metricdefinitions/read | Чтение определений метрик |
> | Microsoft.Insights/metrics/read | Метрик считано |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/operations/read | Возвращает операции развертывания или выводит их список. |
> | Microsoft.Resources/subscriptions/operationresults/read | Возвращает результаты операции подписки. |
> | Microsoft.Resources/subscriptions/read | Возвращает список подписок. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="cosmos-db-account-reader-role"></a>Роль читателя учетных записей Cosmos DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет считывать данные учетных записей Azure Cosmos DB. Сведения об управлении учетными записями Azure Cosmos DB см. в разделе [Участник учетной записи DocumentDB](#documentdb-account-contributor). |
> | **Id** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей. Может считывать данные о разрешениях, предоставленных каждому пользователю |
> | Microsoft.DocumentDB/*/read | Чтение любой коллекции |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Считывает ключи только для чтения учетной записи базы данных. |
> | Microsoft.Insights/MetricDefinitions/read | Чтение определений метрик |
> | Microsoft.Insights/Metrics/read | Метрик считано |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="cosmos-db-operator"></a>Оператор Cosmos DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять учетными записями Azure Cosmos DB, но не доступом к данным. Предотвращает доступ к ключам учетной записи и строкам подключения. |
> | **Id** | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | **Действия** |  |
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | Microsoft. DocumentDB/databaseAccounts/Реадонликэйс/* |  |
> | Microsoft. DocumentDB/databaseAccounts/Реженератекэй/* |  |
> | Microsoft. DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft. DocumentDB/databaseAccounts/Листконнектионстрингс/* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="cosmosbackupoperator"></a>CosmosBackupOperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет отправлять запрос на восстановление контейнера или базы данных Cosmos DB для учетной записи. |
> | **Id** | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | **Действия** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | Отправка запроса на настройку резервного копирования |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | Отправка запроса восстановления |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="cost-management-contributor"></a>Участник Управления затратами
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет просматривать расходы и управлять конфигурацией затрат (например, бюджеты, экспорты) |
> | **Id** | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | **Действия** |  |
> | Microsoft.Consumption/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft.Billing/billingPeriods/read |  |
> | Microsoft.Resources/subscriptions/read | Возвращает список подписок. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | Microsoft.Advisor/configurations/read | Получение конфигураций |
> | Microsoft.Advisor/recommendations/read | Считывает рекомендации. |
> | Microsoft.Management/managementGroups/read | Вывод списка групп управления для пользователя, прошедшего проверку подлинности. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="cost-management-reader"></a>Читатель Управления затратами
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет просматривать данные о расходах и конфигурации (например, бюджеты, экспорты) |
> | **Id** | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | **Действия** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Billing/billingPeriods/read |  |
> | Microsoft.Resources/subscriptions/read | Возвращает список подписок. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | Microsoft.Advisor/configurations/read | Получение конфигураций |
> | Microsoft.Advisor/recommendations/read | Считывает рекомендации. |
> | Microsoft.Management/managementGroups/read | Вывод списка групп управления для пользователя, прошедшего проверку подлинности. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="data-box-contributor"></a>Участник Data Box
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять всеми данными службы Data Box, кроме предоставления доступа другим пользователям. |
> | **Id** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | Microsoft.Databox/* |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="data-box-reader"></a>Читатель Data Box
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять службой Data Box, но не позволяет создавать заказы и менять их порядок, а также предоставлять доступ другим пользователям. |
> | **Id** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Databox/*/read |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | Выводит список незашифрованных учетных данных, связанных с заказом. |
> | Microsoft.Databox/locations/availableSkus/action | Этот метод возвращает список доступных номеров SKU. |
> | Microsoft. Датабокс/Locations/Валидатеаддресс/Action | Проверяет адрес доставки и предлагает альтернативные адреса, если они доступны. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="data-factory-contributor"></a>Участник Фабрики данных
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Создание фабрик данных и управление ими, а также их дочерними ресурсами. |
> | **Id** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.DataFactory/dataFactories/* | Создание фабрик данных и дочерних ресурсов внутри их, а также управление ими. |
> | Microsoft.DataFactory/factories/* | Создание фабрик данных и дочерних ресурсов внутри их, а также управление ими. |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="data-lake-analytics-developer"></a>Разработчик Data Lake Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет отправлять, отслеживать задания и управлять ими, но не позволяет создавать или удалять учетные записи Data Lake Analytics. |
> | **Id** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | Удаляет учетную запись Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Предоставляет разрешения на отмену заданий, отправленных другими пользователями. |
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
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="data-purger"></a>Модуль очистки данных
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может очистить данные аналитики |
> | **Id** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Действия** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Очистка данных Application Insights |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft.OperationalInsights/workspaces/purge/action | Удаление указанных данных из рабочей области. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="devtest-labs-user"></a>Пользователь DevTest Labs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет подключать, запускать, перезапускать виртуальные машины и завершать их работу в Azure DevTest Labs. |
> | **Id** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Compute/availabilitySets/read | Возвращает свойства группы доступности. |
> | Microsoft.Compute/virtualMachines/*/read | Чтение свойств виртуальной машины (размеры виртуальных машин, состояние среды выполнения, расширения виртуальных машин, и т. д.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Завершает работу виртуальной машины и освобождает ее вычислительные ресурсы. |
> | Microsoft.Compute/virtualMachines/read | Возвращает свойства виртуальной машины. |
> | Microsoft.Compute/virtualMachines/restart/action | Перезапуск виртуальной машины |
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
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="dns-zone-contributor"></a>Участник зоны DNS
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять зонами DNS и наборами записей в Azure DNS, но не тем, кому они будут доступны. |
> | **Id** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.Network/dnsZones/* | Создание зон и записей DNS и управление ими. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="documentdb-account-contributor"></a>Участник учетной записи DocumentDB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может управлять учетными записями Azure Cosmos DB Служба Azure Cosmos DB раньше называлась DocumentDB. |
> | **Id** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.DocumentDb/databaseAccounts/* | Создание учетных записей Azure Cosmos DB и управление ими |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="eventgrid-eventsubscription-contributor"></a>Участник EventGrid EventSubscription
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять операциями с подписками на события Сетки событий. |
> | **Id** | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.EventGrid/eventSubscriptions/* |  |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Создание списка подписок на глобальные события по типу темы |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Создание списка подписок на события в регионе |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Создание списка подписок на события в регионе по типу темы |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="eventgrid-eventsubscription-reader"></a>Читатель EventGrid EventSubscription
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет получить доступ на чтение к подпискам на события Сетки событий. |
> | **Id** | 2414bbcf-6497-4faf-8c65-045460748405 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.EventGrid/eventSubscriptions/read | Чтение подписки |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Создание списка подписок на глобальные события по типу темы |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Создание списка подписок на события в регионе |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Создание списка подписок на события в регионе по типу темы |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="hdinsight-cluster-operator"></a>Оператор кластера HDInsight
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет читать и изменять конфигурации кластера HDInsight. |
> | **Id** | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | **Действия** |  |
> | Microsoft. HDInsight/*/Реад |  |
> | Microsoft. HDInsight/Clusters/Жетгатевайсеттингс/действие | Получение параметров шлюза для кластера HDInsight |
> | Microsoft. HDInsight/Clusters/Упдатегатевайсеттингс/действие | Обновление параметров шлюза для кластера HDInsight |
> | Microsoft. HDInsight/кластеры/конфигурации/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Resources/deployments/operations/read | Возвращает операции развертывания или выводит их список. |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="hdinsight-domain-services-contributor"></a>Участник доменных служб HDInsight
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет читать, создавать, изменять и удалять операции, связанные с доменными службами, необходимыми для Корпоративного пакета безопасности HDInsight |
> | **Id** | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | **Действия** |  |
> | Microsoft.AAD/*/read |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="intelligent-systems-account-contributor"></a>Участник учетной записи интеллектуальных систем
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять учетными записями интеллектуальных систем, но не доступом к ним. |
> | **Id** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.IntelligentSystems/accounts/* | Создание учетных записей интеллектуальных систем и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="key-vault-contributor"></a>Участник Key Vault
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять хранилищами ключей, но не доступом к ним. |
> | **Id** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Очищает обратимо удаленное хранилище Key Vault. |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="lab-creator"></a>Создатель лаборатории
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет создавать, администрировать и удалять управляемые лаборатории в учетных записях Служб лабораторий Azure. |
> | **Id** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Создание лаборатории в учетной записи лаборатории. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Позволяет получать сведения о доступности по регионам для каждой категории размеров, настроенной в учетной записи лаборатории. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="log-analytics-contributor"></a>участник Log Analytics.
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Участник Log Analytics может считывать все данные мониторинга и изменять параметры мониторинга. Изменение параметров мониторинга подразумевает добавление расширений в виртуальные машины, чтение ключей учетной записи хранения для настройки коллекции журналов в службе хранилища Azure, создание и настройку учетных записей службы автоматизации, добавление решений и настройку диагностики Azure во всех ресурсах Azure. |
> | **Id** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Отображает ключи доступа для учетных записей хранения. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Insights/diagnosticSettings/* | Создание, обновление или считывание параметра диагностики для сервера анализа данных. |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="log-analytics-reader"></a>Читатель Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Читатель Log Analytics может просматривать все данные мониторинга, выполнять по ним поиск и просматривать параметры мониторинга, в том числе конфигурацию системы диагностики Azure для всех ресурсов Azure. |
> | **Id** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Поиск с помощью нового механизма. |
> | Microsoft.OperationalInsights/workspaces/search/action | Выполняет поисковый запрос. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Извлекает открытые ключи для рабочей области. Эти ключи используются для подключения агентов Microsoft Operational Insights к рабочей области. |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="logic-app-contributor"></a>Создатель приложений логики
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять приложениями логики, но не изменяет доступ к ним. |
> | **Id** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Отображает ключи доступа для учетных записей хранения. |
> | Microsoft.ClassicStorage/storageAccounts/read | Возвращает учетную запись хранения для заданной учетной записи. |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Insights/diagnosticSettings/* | Создание, обновление или считывание параметра диагностики для сервера анализа данных. |
> | Microsoft.Insights/logdefinitions/* | Это разрешение необходимо пользователям, которым требуется доступ к журналам действия на портале. Получение списка категорий журнала в журнале действий. |
> | Microsoft.Insights/metricDefinitions/* | Чтение определений метрик (вывод списка доступных типов метрик для ресурса). |
> | Microsoft.Logic/* | Управляет ресурсами Logic Apps. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/operationresults/read | Возвращает результаты операции подписки. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
> | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | Microsoft.Web/connectionGateways/* | Создает шлюз подключения и управляет им. |
> | Microsoft.Web/connections/* | Создает подключение и управляет им. |
> | Microsoft.Web/customApis/* | Создает настраиваемый API и управляет им. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Возвращает свойства плана службы приложений. |
> | Microsoft.Web/sites/functions/listSecrets/action | Выводит список секретов для функций веб-приложений. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="logic-app-operator"></a>Оператор приложений логики
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет читать, включать и отключать приложения логики, но не изменять и не обновлять их. |
> | **Id** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/*/read | Считывание правил оповещений Insights. |
> | Microsoft.Insights/diagnosticSettings/*/read | Получает параметры диагностики для Logic Apps. |
> | Microsoft.Insights/metricDefinitions/*/read | Получает доступные метрики для Logic Apps. |
> | Microsoft.Logic/*/read | Считывает ресурсы Logic Apps. |
> | Microsoft.Logic/workflows/disable/action | Отключает рабочий процесс. |
> | Microsoft.Logic/workflows/enable/action | Включает рабочий процесс. |
> | Microsoft.Logic/workflows/validate/action | Проверяет рабочий процесс. |
> | Microsoft.Resources/deployments/operations/read | Возвращает операции развертывания или выводит их список. |
> | Microsoft.Resources/subscriptions/operationresults/read | Возвращает результаты операции подписки. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | Microsoft.Web/connectionGateways/*/read | Считывает шлюзы подключения. |
> | Microsoft.Web/connections/*/read | Считывает подключения. |
> | Microsoft.Web/customApis/*/read | Считывает настраиваемый API. |
> | Microsoft.Web/serverFarms/read | Возвращает свойства плана службы приложений. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="managed-application-operator-role"></a>Роль оператора управляемого приложения
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет читать и выполнять действия с ресурсами управляемого приложения. |
> | **Id** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | Microsoft.Solutions/applications/read | Извлечение списка приложений. |
> | Microsoft. Solutions/*/Action |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="managed-applications-reader"></a>Читатель Управляемых приложений
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет считывать ресурсы в управляемом приложении и запрашивать доступ JIT. |
> | **Id** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Solutions/jitRequests/* |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="managed-identity-contributor"></a>Участник управляемого удостоверения
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Создание, чтение, обновление и удаление пользовательских удостоверений. |
> | **Id** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Действия** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/read | Получение существующего пользовательского удостоверения. |
> | Microsoft.ManagedIdentity/userAssignedIdentities/write | Создание существующего пользовательского удостоверения или обновление связанных с ним тегов. |
> | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Удаление существующего пользовательского удостоверения. |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="managed-identity-operator"></a>Оператор управляемого удостоверения
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Чтение и назначение пользовательских удостоверений. |
> | **Id** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Действия** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="management-group-contributor"></a>Участник группы управления
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Роль участника группы управления |
> | **Id** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **Действия** |  |
> | Microsoft.Management/managementGroups/delete | Удаление группы управления. |
> | Microsoft.Management/managementGroups/read | Вывод списка групп управления для пользователя, прошедшего проверку подлинности. |
> | Microsoft.Management/managementGroups/subscriptions/delete | Отмена связывания подписки с группой управления. |
> | Microsoft.Management/managementGroups/subscriptions/write | Связывание существующей подписки с группой управления. |
> | Microsoft.Management/managementGroups/write | Создание или обновление группы управления. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="management-group-reader"></a>Читатель группы управления
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Роль читателя группы управления |
> | **Id** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **Действия** |  |
> | Microsoft.Management/managementGroups/read | Вывод списка групп управления для пользователя, прошедшего проверку подлинности. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="monitoring-contributor"></a>Участник мониторинга
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может читать все данные мониторинга и изменять параметры мониторинга. Ознакомьтесь также со статьей [Приступая к работе с ролями, разрешениями и системой безопасности с помощью Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Чтение, запись и удаление правила генерации оповещений. |
> | Microsoft.Insights/components/* | Чтение, запись и удаление компонентов Application Insights. |
> | Microsoft.Insights/DiagnosticSettings/* | Чтение, запись и удаление параметров диагностики. |
> | Microsoft.Insights/eventtypes/* | Вывод списка событий журнала действий (событий управления) в подписке. Это разрешение применяется для доступа к журналу действий посредством кода или портала. |
> | Microsoft.Insights/LogDefinitions/* | Это разрешение необходимо пользователям, которым требуется доступ к журналам действия на портале. Получение списка категорий журнала в журнале действий. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Чтение определений метрик (вывод списка доступных типов метрик для ресурса). |
> | Microsoft.Insights/Metrics/* | Чтение метрик для ресурса. |
> | Microsoft.Insights/Register/Action | Регистрация поставщика Microsoft Insights |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Чтение, запись и удаление веб-тестов Application Insights. |
> | Microsoft. Insights/книги/* |  |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Чтение, запись и удаление пакетов решений log Analytics. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Чтение, запись и удаление сохраненных поисков log Analytics. |
> | Microsoft.OperationalInsights/workspaces/search/action | Выполняет поисковый запрос. |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Извлекает открытые ключи для рабочей области. Эти ключи используются для подключения агентов Microsoft Operational Insights к рабочей области. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Чтение, запись и удаление конфигураций аналитики хранилища log Analytics. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | Microsoft.WorkloadMonitor/monitors/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | Microsoft. Алертсманажемент/Смартдетекторалертрулес/* |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="monitoring-metrics-publisher"></a>Издатель метрик мониторинга
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Включает публикацию метрик ресурсов Azure |
> | **Id** | 3913510d-42f4-4e42-8a64-420c390055eb |
> | **Действия** |  |
> | Microsoft.Insights/Register/Action | Регистрация поставщика Microsoft Insights |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft.Insights/Metrics/Write | Запись показателей |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="monitoring-reader"></a>Читатель данных мониторинга
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может читать все данные мониторинга (метрики, журналы и т. д.). Ознакомьтесь также со статьей [Приступая к работе с ролями, разрешениями и системой безопасности с помощью Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | Microsoft.OperationalInsights/workspaces/search/action | Выполняет поисковый запрос. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="network-contributor"></a>Участник сетей
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять сетями, но не доступом к ним. |
> | **Id** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.Network/* | Создание сетей и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="new-relic-apm-account-contributor"></a>Участник учетной записи New Relic APM
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять учетными записями и приложениями New Relic Application Performance Management, но не доступом к ним. |
> | **Id** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="reader-and-data-access"></a>Модуль чтения и доступ к данным
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет просматривать все данные, но не позволит удалить или создать учетную запись хранения или содержащий ресурс. Он также предоставит доступ на чтение и запись для всех данных, содержащихся в учетной записи хранения через доступ к ключам учетной записи хранения. |
> | **Id** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Действия** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
> | Microsoft. Storage/storageAccounts/Листаккаунтсас/действие | Возвращает маркер SAS для указанной учетной записи хранения. |
> | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="redis-cache-contributor"></a>Участник кэшей Redis
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять кэшем Redis, но не доступом к нему. |
> | **Id** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Cache/redis/* | Создание кэшей Redis и управление ими |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="resource-policy-contributor-preview"></a>Участник политики ресурсов (предварительная версия)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Обратное заполнение пользователей из EA с правами на создание и изменение политики ресурсов, создание запросов в службу поддержки и чтение ресурсов и иерархии (предварительная версия). |
> | **Id** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | Microsoft.Authorization/policyassignments/* | Создание назначений политик и управление ими |
> | Microsoft.Authorization/policydefinitions/* | Создание определений политик и управление ими |
> | Microsoft.Authorization/policysetdefinitions/* | Создание наборов политик и управление ими |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="scheduler-job-collections-contributor"></a>Участник коллекций заданий планировщика
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять коллекциями заданий планировщика, но не доступом к ним. |
> | **Id** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Scheduler/jobcollections/* | Создание коллекциями заданий и управление ими |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="search-service-contributor"></a>Участник службы поиска
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять службами поиска, но не доступом к ним. |
> | **Id** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Search/searchServices/* | Создание служб поиска и управление ими |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="security-admin"></a>Администратор системы безопасности
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Только в Центре безопасности. Может просматривать политики и состояния безопасности, изменять политики безопасности, просматривать оповещения и рекомендации, а также закрывать предупреждения и рекомендации. |
> | **Id** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Authorization/policyAssignments/* | Создание назначений политик и управление ими |
> | Microsoft.Authorization/policyDefinitions/* | Создание определений политик и управление ими |
> | Microsoft.Authorization/policySetDefinitions/* | Создание наборов политик и управление ими |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.Management/managementGroups/read | Вывод списка групп управления для пользователя, прошедшего проверку подлинности. |
> | Microsoft.operationalInsights/workspaces/*/read | Просмотр данных log Analytics |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Security/* |  |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="security-manager-legacy"></a>Диспетчер безопасности (устаревший)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Это устаревшая роль. Используйте роль администратора безопасности |
> | **Id** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.ClassicCompute/*/read | Чтение сведений о конфигурации классических виртуальных машин |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Запись сведений о конфигурации классических виртуальных машин |
> | Microsoft.ClassicNetwork/*/read | Чтение сведений о конфигурации для классической сети |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Security/* | Создание компонентов и политик безопасности и управление ими |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="security-reader"></a>Читатель сведений о безопасности
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Только в Центре безопасности. Может просматривать оповещения и рекомендации, просматривать политики и состояния безопасности, но не может вносить изменения. |
> | **Id** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.operationalInsights/workspaces/*/read | Просмотр данных log Analytics |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Security/*/read | Чтение компонентов и политик безопасности |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | Microsoft.Management/managementGroups/read | Вывод списка групп управления для пользователя, прошедшего проверку подлинности. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="site-recovery-contributor"></a>Участник Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять службой Site Recovery, за исключением создания хранилищ и назначения ролей. |
> | **Id** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp — внутренняя операция, используемая службой |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocatedStamp является внутренней операцией, используемой службой. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Операция "Обновить сертификат ресурсов" обновляет сертификат учетных данных ресурсов или хранилища. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Создание расширенных сведений, связанных с хранилищем, и управление ими |
> | Microsoft.RecoveryServices/Vaults/read | Операция "Получить хранилище" получает объект, представляющий ресурс Azure типа "vault" |
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
> | Microsoft.RecoveryServices/Vaults/usages/read | Возвращает подробности об использовании для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Операцию "Токен хранилища" можно использовать, чтобы получить токен хранилища для внутренних операций уровня хранилища. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Чтение оповещений для хранилища служб восстановления |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="site-recovery-operator"></a>Оператор Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет выполнять отработку отказа и восстановление размещения, но не другие операции управления Site Recovery. |
> | **Id** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp — внутренняя операция, используемая службой |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocatedStamp является внутренней операцией, используемой службой. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Операция "Получить расширенные сведения" получает расширенные сведения об объекте, представляющие ресурс Azure типа ?vault? |
> | Microsoft.RecoveryServices/Vaults/read | Операция "Получить хранилище" получает объект, представляющий ресурс Azure типа "vault" |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Операцию "Получить результаты операции" можно использовать для получения состояния операции и результата асинхронно отправленной операции |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Операцию "Получить контейнеры" можно использовать для регистрации контейнеров в ресурсе. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Считывание параметров оповещений |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Считывание события |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Проверяет согласованность структуры |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Считывание структуры |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Повторно задает связь шлюза. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Обновление сертификата для структуры. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Считывание сети |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Считывание сетевых сопоставлений |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Считывание контейнеров защиты |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Считывает защищаемые элементы. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Применяет точку восстановления. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Выполняет отработку отказа с фиксацией. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Плановая отработка отказа |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Считывание защищенных элементов |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Считывает точки восстановления для репликации. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Исправляет репликацию. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Повторно защищает защищенный элемент. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Тестовая отработка отказа |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Тестирует очистку отработки отказа. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Отработка отказа |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Обновить службу мобильности |
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
> | Microsoft.RecoveryServices/Vaults/usages/read | Возвращает подробности об использовании для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Операцию "Токен хранилища" можно использовать, чтобы получить токен хранилища для внутренних операций уровня хранилища. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="site-recovery-reader"></a>Читатель Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет просматривать состояние Site Recovery без выполнения других операций управления. |
> | **Id** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp — внутренняя операция, используемая службой |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Операция "Получить расширенные сведения" получает расширенные сведения об объекте, представляющие ресурс Azure типа ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Возвращает оповещения для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | Операция "Получить хранилище" получает объект, представляющий ресурс Azure типа "vault" |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Операцию "Получить результаты операции" можно использовать для получения состояния операции и результата асинхронно отправленной операции |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Операцию "Получить контейнеры" можно использовать для регистрации контейнеров в ресурсе. |
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
> | Microsoft.RecoveryServices/Vaults/usages/read | Возвращает подробности об использовании для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Операцию "Токен хранилища" можно использовать, чтобы получить токен хранилища для внутренних операций уровня хранилища. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="spatial-anchors-account-contributor"></a>Участник учетной записи пространственных привязок
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять пространственными привязками в учетной записи, но не удалять их |
> | **Id** | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | **Действия** |  |
> | *Нет* |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/создание/действие | Создание пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/обнаружение/чтение | Обнаружение ближайших пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/свойства/чтение | Получение свойств пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/запрос/чтение | Определение пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/субмитдиаг/Read | Отправка диагностических данных для повышения качества службы пространственных привязок Azure |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/запись | Обновить свойства пространственных привязок |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="spatial-anchors-account-owner"></a>Владелец учетной записи пространственных привязок
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять пространственными привязками в учетной записи, включая их удаление. |
> | **Id** | 70bbe301-9835-447d-afdd-19eb3167307c |
> | **Действия** |  |
> | *Нет* |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/создание/действие | Создание пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/Delete | Удаление пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/обнаружение/чтение | Обнаружение ближайших пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/свойства/чтение | Получение свойств пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/запрос/чтение | Определение пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/субмитдиаг/Read | Отправка диагностических данных для повышения качества службы пространственных привязок Azure |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/запись | Обновить свойства пространственных привязок |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="spatial-anchors-account-reader"></a>Читатель учетной записи пространственных привязок
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет нахождение и чтение свойств пространственных привязок в учетной записи. |
> | **Id** | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Действия** |  |
> | *Нет* |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/обнаружение/чтение | Обнаружение ближайших пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/свойства/чтение | Получение свойств пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/запрос/чтение | Определение пространственных привязок |
> | Microsoft. Микседреалити/Спатиаланчорсаккаунтс/субмитдиаг/Read | Отправка диагностических данных для повышения качества службы пространственных привязок Azure |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="sql-db-contributor"></a>Участник баз данных SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять базами данных SQL, но не доступом к ним. Кроме того, не позволяет управлять их политиками безопасности или родительскими серверами SQL Server. |
> | **Id** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | Создание баз данных SQL и управление ими |
> | Microsoft.Sql/servers/read | Возвращение списка серверов или получение свойств для указанного сервера. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | Microsoft.Insights/metrics/read | Метрик считано |
> | Microsoft.Insights/metricDefinitions/read | Чтение определений метрик |
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
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="sql-managed-instance-contributor"></a>Участник SQL Управляемый экземпляр
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять управляемыми экземплярами SQL и требуемой конфигурацией сети, но не может предоставлять доступ другим пользователям. |
> | **Id** | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | **Действия** |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft. Network/networkSecurityGroups/* |  |
> | Microsoft. Network/Раутетаблес/* |  |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft. SQL/Манажединстанцес/* |  |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | Microsoft. Network/virtualNetworks/подсети/* |  |
> | Microsoft. Network/virtualNetworks/* |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Insights/metrics/read | Метрик считано |
> | Microsoft.Insights/metricDefinitions/read | Чтение определений метрик |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="sql-security-manager"></a>Диспетчер безопасности SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять политиками безопасности серверов SQL Server и баз данных SQL, но не доступом к ним. |
> | **Id** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение авторизации Майкрософт |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Присоединение к подсети ресурса, например учетной записи хранения или базы данных SQL. Не оповещать. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
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
> | Microsoft.Sql/servers/databases/auditRecords/read | Чтение записей аудита |
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
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="sql-server-contributor"></a>Участник SQL Server
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять серверами SQL Server и базами данных SQL, но не доступом к ним и их политиками безопасности. |
> | **Id** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Создание серверов SQL Server и управление ими |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | Microsoft.Insights/metrics/read | Метрик считано |
> | Microsoft.Insights/metricDefinitions/read | Чтение определений метрик |
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
> | Microsoft.Sql/servers/databases/auditRecords/read | Чтение записей аудита |
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
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="storage-account-contributor"></a>Участник учетных записей хранения
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Разрешает управление учетными записями хранения. Предоставляет доступ к ключу учетной записи, который можно использовать для доступа к данным через авторизацию с общим ключом. |
> | **Id** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Авторизация на чтение всех элементов |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Insights/diagnosticSettings/* | Управление параметрами диагностики |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Присоединение к подсети ресурса, например учетной записи хранения или базы данных SQL. Не оповещать. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Storage/storageAccounts/* | Создание учетных записей хранения и управление ими |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="storage-account-key-operator-service-role"></a>Роль службы оператора ключей учетных записей хранения
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Разрешает перечисление и повторное создание ключей доступа к учетной записи хранения. |
> | **Id** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Действия** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Повторно создает ключи доступа для указанной учетной записи хранения. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="storage-blob-data-contributor"></a>Участник данных BLOB-объекта хранилища
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Чтение, запись и удаление контейнеров службы хранилища Azure и больших двоичных объектов. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Действия** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Удаление контейнера. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Возврат контейнера или списка контейнеров. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Измените метаданные или свойства контейнера. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Возвращает ключ делегирования пользователя для службы BLOB-объектов. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Удалить большой двоичный объект. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Возврат большого двоичного объекта или списка больших двоичных объектов. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Запись в большой двоичный объект. |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="storage-blob-data-owner"></a>Владелец данных BLOB-объекта хранилища
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Предоставляет полный доступ к контейнерам и данным больших двоичных объектов службы хранилища Azure, включая назначение управления доступом POSIX. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | **Действия** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | Полные разрешения на контейнеры. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Возвращает ключ делегирования пользователя для службы BLOB-объектов. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | Полные разрешения на большие двоичные объекты. |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="storage-blob-data-reader"></a>Модуль чтения данных BLOB-объекта хранилища
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Прочтите и перечислите контейнеры и большие двоичные объекты службы хранилища Azure. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Действия** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Возврат контейнера или списка контейнеров. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Возвращает ключ делегирования пользователя для службы BLOB-объектов. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Возврат большого двоичного объекта или списка больших двоичных объектов. |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="storage-blob-delegator"></a>Делегирование BLOB-объектов хранилища
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Получите ключ делегирования пользователя, который затем можно использовать для создания подписи общего доступа для контейнера или большого двоичного объекта, подписанного с помощью учетных данных Azure AD. Дополнительные сведения см. [в разделе Создание SAS для делегирования пользователей](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). |
> | **Id** | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | **Действия** |  |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Возвращает ключ делегирования пользователя для службы BLOB-объектов. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="storage-file-data-smb-share-contributor"></a>Участник общей папки SMB данных файлов хранилища
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Разрешает доступ на чтение, запись и удаление в общих файловых ресурсах службы хранилища Azure через SMB. |
> | **Id** | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | **Действия** |  |
> | *Нет* |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft. Storage/storageAccounts/Филесервицес/общие папки/файлы/чтение | Возвращает файл или папку или список файлов и папок. |
> | Microsoft. Storage/storageAccounts/Филесервицес/общие папки/файлы/запись | Возвращает результат записи файла или создания папки. |
> | Microsoft. Storage/storageAccounts/Филесервицес/общие папки/Files/Delete | Возвращает результат удаления файла или папки. |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="storage-file-data-smb-share-elevated-contributor"></a>Участник хранилища данных файлового ресурса SMB с повышенными правами
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Разрешение на чтение, запись, удаление и изменение разрешений NTFS в общих файловых ресурсах службы хранилища Azure через SMB. |
> | **Id** | a7264617-510b-434b-a828-9731dc254ea7 |
> | **Действия** |  |
> | *Нет* |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft. Storage/storageAccounts/Филесервицес/общие папки/файлы/чтение | Возвращает файл или папку или список файлов и папок. |
> | Microsoft. Storage/storageAccounts/Филесервицес/общие папки/файлы/запись | Возвращает результат записи файла или создания папки. |
> | Microsoft. Storage/storageAccounts/Филесервицес/общие папки/Files/Delete | Возвращает результат удаления файла или папки. |
> | Microsoft. Storage/storageAccounts/Филесервицес/общие папки/Files/модифипермиссионс/Action | Возвращает результат изменения разрешения для файла или папки. |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="storage-file-data-smb-share-reader"></a>Средство чтения общего ресурса SMB с данными файлов хранилища
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Разрешает доступ на чтение к файловому ресурсу Azure через SMB. |
> | **Id** | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | **Действия** |  |
> | *Нет* |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft. Storage/storageAccounts/Филесервицес/общие папки/файлы/чтение | Возвращает файл или папку или список файлов и папок. |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="storage-queue-data-contributor"></a>Участник данных очереди хранилища
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Чтение, запись и удаление очередей службы хранилища Azure и сообщений очереди. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Действия** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Удаление очереди. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Возврат очереди или списка очередей. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Измените метаданные или свойства очереди. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Удаление одного или нескольких сообщений из очереди. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Просмотр или извлечение одного или нескольких сообщений из очереди. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Добавление сообщения в очередь. |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="storage-queue-data-message-processor"></a>Обработчик сообщений данных очереди хранилища
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Просмотр, извлечение и удаление сообщения из очереди службы хранилища Azure. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | **Действия** |  |
> | *Нет* |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Просмотр сообщения. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Получение и удаление сообщения. |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="storage-queue-data-message-sender"></a>Отправитель сообщений очереди хранилища
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Добавление сообщений в очередь службы хранилища Azure. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | **Действия** |  |
> | *Нет* |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Добавление сообщения в очередь. |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="storage-queue-data-reader"></a>Модуль чтения данных очереди хранилища
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Чтение и вывод списка очередей службы хранилища Azure и сообщений очереди. Чтобы узнать, какие действия необходимы для конкретной операции с данными, см. раздел [разрешения на вызов операций с данными BLOB-объектов и очередей](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Действия** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Возвращение очереди или списка очередей. |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Просмотр или извлечение одного или нескольких сообщений из очереди. |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="support-request-contributor"></a>Участник запроса в службу поддержки
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет создавать запросы в службу поддержки и управлять ими. |
> | **Id** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Авторизация на чтение |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="traffic-manager-contributor"></a>Участник диспетчера трафика
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять профилями диспетчера трафика, но не доступом к ним. |
> | **Id** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="user-access-administrator"></a>Администратор доступа пользователей
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять доступом пользователей к ресурсам Azure. |
> | **Id** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | Microsoft.Authorization/* | Управление авторизацией |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="virtual-machine-administrator-login"></a>Имя для входа администратора виртуальной машины
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет просматривать виртуальные машины на портале и входить в систему с правами администратора. |
> | **Id** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Действия** |  |
> | Microsoft.Network/publicIPAddresses/read | Возвращает определение общедоступного IP-адреса. |
> | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Microsoft.Network/loadBalancers/read | Возвращает определение подсистемы балансировки нагрузки. |
> | Microsoft.Network/networkInterfaces/read | Возвращает определение сетевого интерфейса.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft.Compute/virtualMachines/login/action | Вход в систему на виртуальной машине в качестве обычного пользователя. |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Вход в систему на виртуальной машине с правами администратора Windows или привилегированного пользователя Linux. |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="virtual-machine-contributor"></a>Участник виртуальных машин
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять виртуальными машинами, но не доступом к ним и не виртуальной сетью или учетной записью хранения, к которой они подключены. |
> | **Id** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Авторизация на чтение |
> | Microsoft.Compute/availabilitySets/* | Создание групп доступности вычислений и управление ими |
> | Microsoft.Compute/locations/* | Создание расположений вычислений и управление ими |
> | Microsoft.Compute/virtualMachines/* | Создание виртуальных машин и управление ими |
> | Microsoft.Compute/virtualMachineScaleSets/* | Создание наборов масштабирования виртуальных машин и управление ими |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Присоединяет внутренний пул адресов шлюза приложений. Не оповещать. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Присоединяет внутренний пул адресов подсистемы балансировки нагрузки. Не оповещать. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Присоединяется к пулу входящих подключений NAT подсистемы балансировки нагрузки. Не оповещать. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Присоединяет правило NAT для входящего трафика подсистемы балансировки нагрузки. Не оповещать. |
> | Microsoft.Network/loadBalancers/probes/join/action | Позволяет использовать зонды подсистемы балансировки нагрузки. Например, при наличии этого разрешения свойство healthProbe в масштабируемом наборе виртуальных машин может ссылаться на конкретный зонд. Не оповещать. |
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
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Возвращает сведения об объекте защищенного элемента |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Создает элемент, защищенный службой архивации. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Возвращает все политики защиты. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Создает политику защиты. |
> | Microsoft.RecoveryServices/Vaults/read | Операция "Получить хранилище" получает объект, представляющий ресурс Azure типа "vault" |
> | Microsoft.RecoveryServices/Vaults/usages/read | Возвращает подробности об использовании для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/write | Операция "Создать хранилище" создает ресурс Azure типа vault. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
> | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="virtual-machine-user-login"></a>Имя для входа пользователя виртуальной машины
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет просматривать виртуальные машины на портале и входить в систему с правами обычного пользователя. |
> | **Id** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Действия** |  |
> | Microsoft.Network/publicIPAddresses/read | Возвращает определение общедоступного IP-адреса. |
> | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Microsoft.Network/loadBalancers/read | Возвращает определение подсистемы балансировки нагрузки. |
> | Microsoft.Network/networkInterfaces/read | Возвращает определение сетевого интерфейса.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | Microsoft.Compute/virtualMachines/login/action | Вход в систему на виртуальной машине в качестве обычного пользователя. |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="web-plan-contributor"></a>Участник веб-планов
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять веб-планами для веб-сайтов, но не доступом к ним. |
> | **Id** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Авторизация на чтение |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | Microsoft.Web/serverFarms/* | Создание ферм серверов и управление ими |
> | Microsoft. Web/hostingEnvironments/Join/действие | Присоединяет Среда службы приложений |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="website-contributor"></a>Участник веб-сайтов
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять веб-сайтами (не веб-планами), но не доступом к ним. |
> | **Id** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Авторизация на чтение |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Insights/components/* | Создание компонентов Insights и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | Microsoft.Web/certificates/* | Создание сертификатов веб-сайтов и управление ими |
> | Microsoft.Web/listSitesAssignedToHostName/read | Возвращает имена сайтов, назначенные имени узла. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Возвращает свойства плана службы приложений. |
> | Microsoft.Web/sites/* | Создание веб-сайтов и управление ими (создание сайта также требует разрешений на запись к связанному плану службы приложений). |
> | **NotActions** |  |
> | *Нет* |  |
> | **Действия с данными** |  |
> | *Нет* |  |
> | **NotDataActions** |  |
> | *Нет* |  |

## <a name="next-steps"></a>Следующие шаги

- [Пользовательские роли для ресурсов Azure](custom-roles.md)
- [Управление доступом к ресурсам Azure с помощью RBAC и портала Azure](role-assignments-portal.md)
- [Разрешения в центре безопасности Azure](../security-center/security-center-permissions.md)
