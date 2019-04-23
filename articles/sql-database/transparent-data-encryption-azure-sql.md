---
title: Прозрачное шифрование данных для хранилища данных и Базы данных SQL Azure | Документация Майкрософт
description: Обзор прозрачного шифрования данных для хранилища данных и Базы данных SQL. В этом документе описаны преимущества и параметры конфигурации прозрачного шифрования, включая управляемое службой прозрачное шифрование данных и создание собственных ключей.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 04/19/2019
ms.openlocfilehash: 8ed7d144b886cc29592418007b9103b4aa94e8ab
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002618"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>Прозрачное шифрование данных для хранилища данных и Базы данных SQL

Прозрачное шифрование данных (TDE) помогает защищать Базу данных SQL Azure, Управляемый экземпляр Базы данных SQL и хранилище данных Azure от вредоносных действий. Выполняется шифрование и расшифровка базы данных, связанных резервных копий и неактивных файлов журналов транзакций в реальном времени без необходимости изменения приложения. По умолчанию TDE включено для всех вновь развертываемых баз данных SQL Azure. Прозрачное шифрование данных нельзя использовать для шифрования логической базы данных **master** в Базе данных SQL.  База данных **master** содержит объекты, которые необходимы для выполнения операций TDE для пользовательских баз данных.

TDE для Управляемого экземпляра SQL Azure, более старых баз данных службы "База данных SQL Azure" или Хранилища данных SQL Azure необходимо включать вручную.  

При использовании прозрачного шифрования данных хранилище всей базы данных шифруется с помощью симметричного ключа, который называется ключом шифрования базы данных. Этот ключ шифрования базы данных защищен предохранителем прозрачного шифрования данных. Предохранитель представляет собой управляемый службой сертификат (управляемое службой прозрачное шифрование данных) или асимметричный ключ, сохраненный в Azure Key Vault (создание собственных ключей). Предохранитель прозрачного шифрования данных настраивается на уровне сервера для Базы данных SQL Azure и хранилища данных и на уровне экземпляра для Управляемого экземпляра SQL Azure. В этом документе термин *сервер* обозначает как сервер, так и экземпляр, если не указано иное.

При запуске базы данных зашифрованный ключ шифрования базы данных расшифровывается. Далее он используется в работе ядра СУБД SQL Server для расшифровки и повторного шифрования файлов баз данных. С помощью TDE выполняется шифрование и расшифровка ввода-вывода на уровне страниц данных в реальном времени. Каждая страница расшифровывается при считывании в память, а затем снова шифруется перед записью на диск. Общие сведения о прозрачном шифровании данных см. в [этой статье](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

SQL Server на виртуальной машине Azure также может использовать асимметричный ключ из Key Vault. Этапы настройки в этом случае будут отличаться от настройки асимметричного ключа, сохраненного в Базе данных SQL и Управляемом экземпляре Базы данных SQL. Дополнительные сведения см. в статье о [расширенном управлении ключами с помощью Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).

## <a name="service-managed-transparent-data-encryption"></a>Управляемое службой прозрачное шифрование данных

В Azure для TDE ключ шифрования базы данных по умолчанию защищается встроенным сертификатом сервера. Для каждого сервера Azure используется уникальный встроенный сертификат. Если база данных находится в отношении георепликации, ключ родительского сервера базы данных-источника используется как для базы данных-источника, так и для базы данных-получателя. Если две базы данных подключены к одному и тому же серверу, то они также совместно используют один встроенный сертификат. Корпорация Майкрософт автоматически меняет эти сертификаты в соответствии с внутренней политикой безопасности, а корневой ключ защищается внутренним секретным хранилищем Майкрософт.

Корпорация Майкрософт гарантирует простое перемещение ключей и управление ими для георепликации и восстановления.

> [!IMPORTANT]
> По умолчанию все создаваемые базы данных SQL шифруются с помощью управляемого службой прозрачного шифрования данных. Базы данных Управляемого экземпляра SQL Azure, существующие базы данных SQL, созданные до мая 2017 г., и базы данных SQL, созданные путем восстановления, георепликации или копирования, по умолчанию не шифруются.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Управляемое клиентом прозрачное шифрование данных. Создание собственных ключей

[TDE с управляемыми клиентом ключами в Azure Key Vault](transparent-data-encryption-byok-azure-sql.md) позволяет зашифровать ключ шифрования базы данных (DEK) с помощью управляемого пользователем асимметричного ключа, который называется предохранителем TDE.  Обычно это также называется поддержкой создания собственных ключей (BYOK) для прозрачного шифрования данных. В сценарии создания собственных ключей предохранитель TDE хранится в управляемом клиентом экземпляре [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault) — облачной системы Azure для управления внешними ключами. Средство защиты прозрачного шифрования данных может быть [созданы в хранилище ключей или передать в хранилище ключей](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) на устройстве HSM в локальной среде. Ключ шифрования TDE, который хранится на загрузочной странице базы данных, шифруется и расшифровывается с помощью предохранителя TDE. Он хранится в Azure Key Vault и никогда не покидает хранилище ключей.  База данных SQL должна иметь разрешения для хранилища ключей, принадлежащего клиенту, для шифрования и расшифрования DEK. Если права доступа логического сервера SQL к хранилищу ключей отменены, база данных будет недоступна и все данные будут зашифрованы. Для Базы данных SQL Azure предохранитель TDE настраивается на уровне логического сервера SQL и наследуется всеми базами данных, связанными с этим сервером. Для [управляемый экземпляр SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance) (BYOK-функция в предварительной версии), средства защиты TDE задается на уровне экземпляра и наследуется всеми *зашифрованных* баз данных этого экземпляра. В этом документе термин *сервер* обозначает как сервер, так и экземпляр, если не указано иное.

Благодаря интеграции TDE с Azure Key Vault пользователи могут самостоятельно контролировать задачи управления ключами. В Azure Key Vault можно выполнять такие операции, как смена ключей, настройка разрешений для хранилища ключей, резервное копирование ключей, а также включать аудит и отчетность по всем предохранителям TDE. Key Vault поддерживает централизованное управление ключами, использует тщательно отслеживаемые аппаратные модули безопасности (HSM), а также позволяет разделять обязанности по управлению ключами и данными для обеспечения соответствия политикам безопасности.
Дополнительные сведения о прозрачном шифровании данных с интеграцией Azure Key Vault (поддержка создания собственных ключей) для базы данных SQL Azure, управляемый экземпляр SQL (BYOK функция в предварительной версии) и хранилище данных, см. в разделе [прозрачное шифрование данных с ключом Azure Хранилище интеграции](transparent-data-encryption-byok-azure-sql.md).

Чтобы реализовать интеграцию прозрачного шифрования данных с Azure Key Vault с созданием собственных ключей из Key Vault с помощью PowerShell, изучите [это практическое руководство](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Перемещение базы данных, защищенной прозрачным шифрованием данных

Для операций в пределах Azure базу данных не нужно расшифровывать. Параметры TDE прозрачно переносятся из базы данных-источника в базу данных-получатель. Это относится ко всем следующим операциям:

- Геовосстановление
- восстановление на определенный момент времени через интерфейс самообслуживания;
- восстановление удаленной базы данных;
- Активная георепликация
- создание копии базы данных.
- Восстановление файла резервной копии в Управляемом экземпляре Базы данных SQL Azure

> [!IMPORTANT]
> В Управляемом экземпляре Базы данных SQL нельзя вручную создать резервную копию (только для копирования) базы данных, зашифрованной с помощью управляемого службой шифрования TDE. Чтобы переместить такую базу данных в другой Управляемый экземпляр, используйте функцию восстановления до точки во времени.

При экспорте базы данных, защищенной прозрачным шифрованием данных, экспортируемое содержимое базы данных не шифруется. Экспортированное содержимое хранится в незашифрованных BACPAC-файлах. Не забудьте обеспечить достаточную защиту для BACPAC-файлов и включить TDE по завершении импорта базы данных.

Например, когда BACPAC-файл экспортируется из локального экземпляра SQL Server, импортированное содержимое новой базы данных не шифруется автоматически. Когда BACPAC-файл экспортируется в локальный экземпляр SQL Server, новая база данных также не шифруется автоматически.

Единственное исключение — экспорт из базы данных SQL в базу данных SQL. В этом случае для новой базы данных будет включено прозрачное шифрование данных, но сам BACPAC-файл по-прежнему не шифруется.

## <a name="manage-transparent-data-encryption-in-the-azure-portal"></a>Управление прозрачным шифрованием данных на портале Azure

Чтобы настроить TDE на портале Azure, подключитесь от имени владельца, участника или администратора безопасности SQL Azure.

Включение и отключение прозрачного шифрования данных осуществляется на уровне базы данных. Чтобы включить TDE для базы данных, войдите на [портал Azure](https://portal.azure.com) с учетной записью администратора или участника Azure. Настройки прозрачного шифрования данных представлены в разделе информации о пользовательской базе данных. По умолчанию используется управляемое службой прозрачное шифрование данных. Для сервера, который содержит базу данных, автоматически создается сертификат TDE. Для Управляемого экземпляра Базы данных SQL Azure используйте T-SQL, чтобы включить и отключить прозрачное шифрование данных для базы данных.

![Управляемое службой прозрачное шифрование данных](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

Главный ключ прозрачного шифрования данных ( также называется предохранителем прозрачного шифрования данных) задается на уровне сервера. Чтобы использовать TDE с поддержкой создания собственных ключей и защитить базы данных с помощью ключа, сохраненного в Key Vault, откройте параметры TDE для своего сервера.

![Прозрачное шифрование данных с поддержкой создания собственных ключей](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

## <a name="manage-transparent-data-encryption-by-using-powershell"></a>Управление прозрачным шифрованием данных с помощью PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных SQL Azure, но все будущие разработки — для модуля Az.Sql. Для этих командлетов см. в разделе [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле Az и в модуле AzureRm практически идентичны.

Чтобы настроить TDE на портале Azure, подключитесь от имени владельца, участника или администратора безопасности SQL Azure.

### <a name="cmdlets-for-azure-sql-database-and-data-warehouse"></a>Командлеты для Базы данных SQL Azure и хранилища данных

Используйте следующие командлеты для Базы данных SQL Azure и хранилища данных:

| Командлет | ОПИСАНИЕ |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Включение или отключение TDE для базы данных|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Получение сведений о состоянии TDE для базы данных |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Проверка хода шифрования для базы данных |
| [Add-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Добавление ключа Key Vault в экземпляр SQL Server |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Получение ключей Key Vault для сервера Базы данных SQL Azure  |
| [SET-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Настройка предохранителя TDE для экземпляра SQL Server |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Получение предохранителя TDE |
| [Remove-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Удаление ключа Key Vault из экземпляра SQL Server |
|  | |

> [!IMPORTANT]
> Для Управляемого экземпляра Базы данных SQL Azure используйте команду T-SQL [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database), чтобы включить или отключить прозрачное шифрование данных на уровне базы данных, и просмотрите [пример скрипта PowerShell](transparent-data-encryption-byok-azure-sql-configure.md) для управления прозрачным шифрованием данных на уровне экземпляра.

## <a name="manage-transparent-data-encryption-by-using-transact-sql"></a>Управление прозрачным шифрованием данных с помощью Transact-SQL

Подключитесь к базе данных, указав имя входа администратора или члена роли **dbmanager** в базе данных master.

| Команда | ОПИСАНИЕ |
| --- | --- |
| [ALTER DATABASE (база данных SQL Azure)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF — шифрование или расшифровка базы данных |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Возврат сведений о состоянии шифрования для базы данных и связанных с ней ключей шифрования |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Возврат сведений о состоянии для узла хранилища данных и связанных с ней ключей шифрования |
|  | |

Transact-SQL не позволяет переключиться с предохранителя TDE на ключ из Key Vault. Используйте для этого портал Azure или PowerShell.

## <a name="manage-transparent-data-encryption-by-using-the-rest-api"></a>Управление прозрачным шифрованием данных с помощью REST API

Чтобы настроить TDE через REST API, подключитесь от имени владельца, участника или администратора безопасности SQL Azure.
Используйте следующий набор команд для Базы данных SQL Azure и хранилища данных:

| Команда | ОПИСАНИЕ |
| --- | --- |
|[Создание или обновление сервера](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Добавление удостоверения Azure Active Directory для экземпляра SQL Server (используется для предоставления доступа к Key Vault)|
|[Создание или обновление ключа сервера](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Добавление ключа Key Vault в экземпляр SQL Server|
|[Удаление ключа сервера](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Удаление ключа Key Vault из экземпляра SQL Server|
|[Получение ключей сервера](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Получение определенного ключа Key Vault из экземпляра SQL Server|
|[Список ключей серверов, упорядоченный по серверам](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Получение ключей Key Vault для экземпляра SQL Server |
|[Создание или обновление предохранителя шифрования](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Настройка предохранителя TDE для экземпляра SQL Server|
|[Получение предохранителя шифрования](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Получение предохранителя TDE для экземпляра SQL Server|
|[Список предохранителей шифрования, упорядоченный по серверам](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Получение всех предохранителей TDE для экземпляра SQL Server |
|[Создание или обновление конфигурации TDE](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Включение или отключение TDE для базы данных|
|[Получение конфигурации TDE](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Получение конфигурации TDE для базы данных|
|[Список результатов TDE, упорядоченный по конфигурации](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Получение результата шифрования для базы данных|

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения о прозрачном шифровании данных см. в [этой статье](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Дополнительные сведения о TDE с поддержкой создания собственных ключей для Базы данных SQL Azure, Управляемого экземпляра Базы данных SQL Azure и хранилища данных можно найти в [этой статье](transparent-data-encryption-byok-azure-sql.md).
- Чтобы реализовать TDE с созданием собственных ключей из Key Vault с помощью PowerShell, изучите [это практическое руководство](transparent-data-encryption-byok-azure-sql-configure.md).
- Чтобы больше узнать о Key Vault, перейдите к [странице документации по Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
