---
title: Прозрачное шифрование данных
description: Обзор прозрачного шифрования данных для базы данных S'L и Synapse S'L в Azure Synapse Analytics. В этом документе описаны преимущества и параметры конфигурации прозрачного шифрования, включая управляемое службой прозрачное шифрование данных и создание собственных ключей.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 04/10/2020
ms.openlocfilehash: 87abdb37ff7773b0205a2ce3ee21689a10c459d7
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113546"
---
# <a name="transparent-data-encryption-for-sql-database-and-azure-synapse"></a>Прозрачное шифрование данных для базы данных S'L и Azure Synapse

[Прозрачное шифрование данных (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) помогает защитить базу данных Azure S'L, управляемые экземпляры Azure S'L и Synapse S'L в Azure Synapse Analytics от угрозы вредоносной деятельности в автономном режиме путем шифрования данных в состоянии покоя. Выполняется шифрование и расшифровка базы данных, связанных резервных копий и неактивных файлов журналов транзакций в реальном времени без необходимости изменения приложения. По умолчанию TDE включен для всех недавно развернутых баз данных Azure S'L и должен быть включен вручную для старых баз данных базы данных Azure S'L, Azure S'L Managed Instance или Azure Synapse.

Функция TDE выполняет шифрование и расшифровку ввода-вывода данных в реальном времени на уровне страницы. Каждая страница расшифровывается при считывании в память, а затем снова шифруется перед записью на диск. TDE шифрует хранилище всей базы данных с помощью симметричного ключа, называемого ключом шифрования базы данных (DEK). При запуске базы данных зашифрованный DEK расшифровывается, а затем используется для расшифровки и повторного шифрования файлов базы данных в процессе создания базы данных s'L Server Engine. DEK защищен защитником TDE. TDE-протектор — это либо сервисный сертификат (управляемое сервисом прозрачное шифрование данных), либо асимметричный ключ, хранящийся в [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault) (управляемое клиентом прозрачное шифрование данных). 

Для базы данных Azure S'L и Azure Synapse протектор TDE установлен на логическом уровне сервера S'L и наследуется всеми базами данных, связанными с этим сервером. Для управляемой инстанции Azure S'L (функция BYOK в предварительном просмотре) протектор TDE устанавливается на уровне экземпляра и наследуется всеми зашифрованными базами данных в этом экземпляре. В этом документе термин *сервер* обозначает как сервер, так и экземпляр, если не указано иное.

> [!IMPORTANT]
> Все вновь созданные базы данных Azure S'L шифруются по умолчанию с помощью управляемого сервисом прозрачного шифрования данных. Существующие базы данных S'L, созданные до мая 2017 г., и базы данных СЗЛ, созданные с помощью восстановления, георепликации и копирования баз данных, не шифруются по умолчанию. Существующие базы данных управляемых инстанций, созданные до февраля 2019 года, не шифруются по умолчанию. Управляемые базы данных инстанций, созданные путем восстановления статуса шифрования наследования из источника.

> [!NOTE]
> Прозрачное шифрование данных нельзя использовать для шифрования логической базы данных **master** в Базе данных SQL.  База данных **master** содержит объекты, которые необходимы для выполнения операций TDE для пользовательских баз данных.


## <a name="service-managed-transparent-data-encryption"></a>Управляемое службой прозрачное шифрование данных

В Azure настройка TDE по умолчанию заключается в том, что DEK защищен встроенным серверным сертификатом. Встроенный сертификат сервера уникален для каждого сервера, а используемый алгоритм шифрования — AES 256. Если база данных находится в георепликтионном отношении, как первичные, так и геовторичные базы данных защищены ключом родительского сервера основной базы данных. Если две базы данных подключены к одному и тому же серверу, то они также совместно используют один встроенный сертификат.  Корпорация Майкрософт автоматически меняет эти сертификаты в соответствии с внутренней политикой безопасности, а корневой ключ защищается внутренним секретным хранилищем Майкрософт.  Клиенты могут проверить соответствие базы данных S'L внутренним политикам безопасности в независимых аудиторских отчетах сторонних групп, доступных в [Trust Center Майкрософт.](https://servicetrust.microsoft.com/)

Корпорация Майкрософт гарантирует простое перемещение ключей и управление ими для георепликации и восстановления.


## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Управляемое клиентом прозрачное шифрование данных. Создание собственных ключей

TDE, управляемый клиентами, также называется поддержкой To Your Own Key (BYOK) для TDE. В этом сценарии TDE Protector, который шифрует DEK, является асимметричным ключом, управляемым клиентом, который хранится в принадлежащем клиенту и управляемом Azure Key Vault (система управления внешними ключами Azure) и никогда не покидает хранилище ключей. TDE Protector может быть [сгенерирован хранилищем ключей или перенесен в хранилище ключей](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) с устройства безопасности оборудования (HSM). База данных SQL должна иметь разрешения для хранилища ключей, принадлежащего клиенту, для шифрования и расшифрования DEK. Если разрешения логического сервера S'L на хранилище ключей будут аннулированы, база данных будет недоступна, а все данные зашифрованы

Благодаря интеграции TDE с Azure Key Vault пользователи могут самостоятельно контролировать задачи управления ключами. В Azure Key Vault можно выполнять такие операции, как смена ключей, настройка разрешений для хранилища ключей, резервное копирование ключей, а также включать аудит и отчетность по всем предохранителям TDE. Key Vault обеспечивает центральное управление ключами, использует строго контролируемые HSMs и позволяет разделить обязанности между управлением ключами и данными, чтобы помочь соответствовать требованиям политик безопасности.
Чтобы узнать больше о BYOK для базы данных Azure S'L и Azure Synapse, [см.](transparent-data-encryption-byok-azure-sql.md)

Чтобы начать использовать TDE с интеграцией Azure Key Vault, смотрите, как можно включить [прозрачное шифрование данных, используя свой собственный ключ от Key Vault.](transparent-data-encryption-byok-azure-sql-configure.md)

## <a name="move-a-transparent-data-encryption-protected-database"></a>Перемещение базы данных, защищенной прозрачным шифрованием данных

Для операций в пределах Azure базу данных не нужно расшифровывать. Параметры TDE базы данных-источника прозрачно наследуются целевым объектом. Это относится ко всем следующим операциям:

- геовосстановлением;
- восстановление на определенный момент времени через интерфейс самообслуживания;
- восстановление удаленной базы данных;
- Активная георепликация
- создание копии базы данных.
- Восстановление файла резервной копии в Управляемом экземпляре Базы данных SQL Azure

> [!IMPORTANT]
> В Управляемом экземпляре Базы данных SQL нельзя вручную создать резервную копию (только для копирования) базы данных, зашифрованной с помощью управляемого службой шифрования TDE. Чтобы переместить такую базу данных в другой Управляемый экземпляр, используйте функцию восстановления до точки во времени.

При экспорте защищенной TDE базой данных экспортируемое содержимое базы данных не шифруется. Экспортированное содержимое хранится в незашифрованных BACPAC-файлах. Убедитесь в том, чтобы защитить файлы BACPAC надлежащим образом и включить TDE после импорта новой базы данных закончена.

Например, когда BACPAC-файл экспортируется из локального экземпляра SQL Server, импортированное содержимое новой базы данных не шифруется автоматически. Когда BACPAC-файл экспортируется в локальный экземпляр SQL Server, новая база данных также не шифруется автоматически.

Единственное исключение — экспорт из базы данных SQL в базу данных SQL. TDE включен в новую базу данных, но сам файл BACPAC до сих пор не зашифрован.


## <a name="manage-transparent-data-encryption"></a>Управление прозрачным шифрованием данных
# <a name="portal"></a>[Портал](#tab/azure-portal)
Управление TDE на портале Azure.

Чтобы настроить TDE через портал Azure, вы должны быть подключены как владелец Azure, автор или менеджер безопасности S'L.

Вы включаете и выключаете TDE на уровне базы данных. Чтобы включить TDE в базу данных, перейдите на [портал Azure](https://portal.azure.com) и войдите в систему с учетной записью администратора или вкладчика Azure. Найдите параметры TDE для своей пользовательской базы данных. По умолчанию используется управляемое службой прозрачное шифрование данных. Сертификат TDE автоматически генерируется для сервера, который содержит базу данных. Для azure S'L Управляемые экземпляры используют T-S'L для включании и выключения TDE в базе данных.

![Управляемое службой прозрачное шифрование данных](./media/transparent-data-encryption-azure-sql/service-managed-transparent-data-encryption.png)  

Вы устанавливаете мастер-ключ TDE, известный как протектор TDE, на уровне сервера. Чтобы использовать TDE при поддержке BYOK и защитить базы данных ключом от Key Vault, откройте настройки TDE под сервером.

![Прозрачное шифрование данных с поддержкой создания собственных ключей](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Управление TDE с помощью PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных Azure S'L, но все будущие разработки предназначены для модуля Az.Sql. Для этих cmdlets, см [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле Az и в модулях Azrm существенно идентичны.

Для настройки TDE с помощью PowerShell нужно подключиться в качестве владельца Azure, участника или диспетчера безопасности SQL.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Cmdlets для базы данных Azure S'L и Azure Synapse

Используйте следующие cmdlets для базы данных Azure S'L и Azure Synapse:

| Командлет | Описание |
| --- | --- |
| [Set-AzSqlDatabaseПроянистогошифрованияДанныеДанныеДанные](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Включение или отключение TDE для базы данных|
| [Get-AzSqlDatabaseПроянистогошифрованияДанныеДанныеДанные](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Получение сведений о состоянии TDE для базы данных |
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Проверка хода шифрования для базы данных |
| [Адд-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Добавление ключа Key Vault в экземпляр SQL Server |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Получение ключей Key Vault для сервера Базы данных SQL Azure  |
| [Set-AzSqlServerПрозрачныйПротекторШифрованияШифрования](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Настройка предохранителя TDE для экземпляра SQL Server |
| [Get-AzSqlServerTransparentEncryptionEncryptionEncryptionПротектор](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Получение предохранителя TDE |
| [Удалить-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Удаление ключа Key Vault из экземпляра SQL Server |
|  | |

> [!IMPORTANT]
> Для управления Azure S'L используйте команду T-S'L [ALTER DATABASE,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) чтобы включить и выключить TDE на уровне базы данных, а также проверить [образец сценария PowerShell](transparent-data-encryption-byok-azure-sql-configure.md) для управления TDE на уровне экземпляра.

# <a name="transact-sql"></a>[Трансакт-СЗЛ](#tab/azure-TransactSQL)
Управление TDE с помощью Transact-S'L.

Подключитесь к базе данных, указав имя входа администратора или члена роли **dbmanager** в базе данных master.

| Get-Help | Описание |
| --- | --- |
| [CREATE DATABASE (база данных SQL Azure)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF — шифрование или расшифровка базы данных |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Возврат сведений о состоянии шифрования для базы данных и связанных с ней ключей шифрования |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Возвращает информацию о состоянии шифрования каждого узла Azure Synapse и связанных с ним ключей шифрования базы данных |
|  | |

Вы не можете переключить протектор TDE на ключ от Key Vault с помощью Transact-S'L. Используйте для этого портал Azure или PowerShell.

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)
Управление TDE с помощью REST API.

Чтобы настроить TDE через REST API, вы должны быть подключены как владелец Azure, вкладчик или менеджер безопасности S'L.
Используйте следующий набор команд для базы данных Azure S'L и Azure Synapse:

| Get-Help | Описание |
| --- | --- |
|[Создание или обновление сервера](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Добавление удостоверения Azure Active Directory для экземпляра SQL Server (используется для предоставления доступа к Key Vault)|
|[Создание или обновление ключа сервера](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Добавление ключа Key Vault в экземпляр SQL Server|
|[Удаление ключа сервера](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Удаление ключа Key Vault из экземпляра SQL Server|
|[Получение ключей сервера](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Получение определенного ключа Key Vault из экземпляра SQL Server|
|[Список ключей серверов, упорядоченный по серверам](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Получение ключей Key Vault для экземпляра SQL Server |
|[Создание или обновление предохранителя шифрования](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Устанавливает протектор TDE для экземпляра сервера S'L|
|[Получение предохранителя шифрования](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Получает протектор TDE для экземпляра сервера S'L|
|[Список предохранителей шифрования, упорядоченный по серверам](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Получает протекторы TDE для экземпляра S'L Server |
|[Создание или обновление конфигурации TDE](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Позволяет или отклоняет TDE для базы данных|
|[Получение конфигурации TDE](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Получает конфигурацию TDE для базы данных|
|[Список результатов TDE, упорядоченный по конфигурации](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Получение результата шифрования для базы данных|

## <a name="see-also"></a>См. также:
- SQL Server на виртуальной машине Azure также может использовать асимметричный ключ из Key Vault. Этапы настройки в этом случае будут отличаться от настройки асимметричного ключа, сохраненного в Базе данных SQL и Управляемом экземпляре Базы данных SQL. Дополнительные сведения см. в статье о [расширенном управлении ключами с помощью Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).
- Для общего описания TDE [см.](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)
- Чтобы узнать больше о TDE с помощью поддержки BYOK для базы данных Azure S'L, управляемых экземпляров Azure s'L и Azure Synapse, [см. Прозрачное шифрование данных с поддержкой Bring Your Own Key.](transparent-data-encryption-byok-azure-sql.md)
- Чтобы начать использовать TDE с поддержкой Bring Your Own Key, см. руководство по использованию [прозрачного шифрования данных с помощью собственного ключа от Key Vault.](transparent-data-encryption-byok-azure-sql-configure.md)
- Для получения дополнительной информации о Key Vault [см.](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)
