---
title: Импорт или экспорт базы данных S'L
description: Импортируйте или экспортируйте базу данных Azure S'L, не позволяя службам Azure получить доступ к серверу.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: 61a85b2554bbd69541b3081f72525d2b7deed625
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529233"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Импортировать или экспортировать базу данных Azure S'L, не позволяя службам Azure получить доступ к серверу

В этой статье показано, как импортировать или экспортировать базу данных Azure S'L при установке *службы «Разрешить Лазурный»* на *сервере Azure* S'L. Рабочий процесс использует виртуальную машину Azure для выполнения SqlPackage для выполнения операции импорта или экспорта.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портал Azure](https://portal.azure.com/).

## <a name="create-the-azure-virtual-machine"></a>Создание виртуальной машины Azure

Создайте виртуальную машину Azure, выбрав кнопку **«Развертывание в Azure».**

Этот шаблон позволяет развернуть простую виртуальную машину Windows, используя несколько различных вариантов для версии Windows, используя последнюю исправленную версию. Это позволит развернуть VM размера A2 в местоположении группы ресурсов и вернуть полностью квалифицированное доменное имя VM.
<br><br>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Для получения дополнительной [информации см.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)


## <a name="connect-to-the-virtual-machine"></a>Подключитесь к виртуальной машине

Следующие шаги показывают, как подключиться к виртуальному компьютеру с помощью удаленного соединения рабочего стола.

1. После завершения развертывания перейдите в ресурс виртуальной машины.

    ![ВМ](./media/import-export-from-vm/vm.png)  

2. Выберите **Подключите**.

   Для виртуальной машины отобразится форма файла протокола удаленного рабочего стола (RDP-файл), а также общедоступный IP-адрес и номер порта.

   ![Форма RDP](./media/import-export-from-vm/rdp.png)  

3. Выберите **Скачать RDP файл**.

   > [!NOTE]
   > Вы также можете подключаться к виртуальной машине по протоколу SSH.

4. Закройте форму **Connect to virtual machine** (Подключение к виртуальной машине).
5. Чтобы подключиться к виртуальной машине, откройте скачанный RDP-файл.
6. При появлении запроса выберите **Подключиться**. На компьютере Mac вам понадобится клиент RDP, например [Remote Desktop Client](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) из Mac App Store.

7. Введите имя пользователя и пароль, указанные при создании виртуальной машины, и нажмите кнопку **ОК**.

8. При входе в систему может появиться предупреждение о сертификате. Чтобы продолжить процесс подключения, выберите **Да** или **Продолжить**.



## <a name="install-sqlpackage"></a>Установка SqlPackage

[Скачать и установить последнюю версию SlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).




Для получения дополнительной [информации см.](https://docs.microsoft.com/sql/tools/sqlpackage)

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>Создайте правило брандмауэра, чтобы позволить VM доступ к базе данных

Добавьте общедоступный IP-адрес виртуальной машины в брандмауэр серверной системы базы данных S'L.

Следующие шаги создают правило IP-сообщения на уровне сервера для общедоступного IP-адреса вашей виртуальной машины и позволяют подключение к виртуальной машине.

1. Выберите **базы данных s'L** из меню слева, а затем выберите базу данных на странице **баз данных S'L.** Открывается страница обзора базы данных, показывающая полностью квалифицированное имя сервера **(например, servername.database.windows.net)** и предоставляется варианты дальнейшей конфигурации.

2. Копируйте это полностью квалифицированное имя сервера для использования при подключении к серверу и его базам данных.

   ![имя сервера](./media/sql-database-get-started-portal/server-name.png)

3. На панели инструментов щелкните **Настройка брандмауэра для сервера**. Для сервера базы данных откроется страница **Параметры брандмауэра**.

   ![Правило брандмауэра для IP-адресов на уровне сервера](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Выберите **«Добавить клиентский IP»** на панели инструментов, чтобы добавить общедоступный IP-адрес вашей виртуальной машины в новое правило IP-брандмауэра уровня сервера. С использованием правила брандмауэра для IP-адресов на уровне сервера вы можете открыть порт 1433 для одного IP-адреса или диапазона IP-адресов.

5. Щелкните **Сохранить**. Правило IP-пространства IP-класса создается для открытого порта IP-адреса вашей виртуальной машины, открывающего порт 1433 на сервере базы данных S'L.

6. Закройте страницу **Параметры брандмауэра**.



## <a name="export-a-database-using-sqlpackage"></a>Экспорт базы данных с помощью SqlPackage

Экспорт базы данных SQL с помощью программы командной строки [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) описывается в разделе о [параметрах и свойствах операции экспорта](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). Утилита SqlPackage поставляется с последними версиями [студии управления серверами s'L](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) и [инструментов данных сервера S'L,](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)или вы можете скачать последнюю версию [SlPackage.](https://docs.microsoft.com/sql/tools/sqlpackage-download)

Мы рекомендуем использовать утилиту SqlPackage для масштабирования и производительности в большинстве производственных сред. Сведения о миграции из SQL Server в Базу данных SQL Azure с использованием BACPAC-файлов см. в [блоге группы консультирования клиентов SQL Server](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

В этом примере показано, как экспортировать базу данных с помощью SqlPackage.exe с помощью универсальной аутентификации Active Directory. Замените значения, которые специфичны для вашей среды.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```




## <a name="import-a-database-using-sqlpackage"></a>Импорт базы данных с помощью SqlPackage

Импорт базы данных SQL Server с помощью программы командной строки [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) описывается в разделе [Параметры и свойства импорта](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage имеет новейшую [студию управления серверами s'L](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) и [инструменты для обработки данных серверов S'L.](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) Вы также можете скачать последнюю версию [SlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

Вместо портала Azure рекомендуется использовать SqlPackage для масштабирования и обеспечения производительности в большинстве рабочих сред. Сведения о миграции из SQL Server в Базу данных SQL Azure с помощью файлов `BACPAC` см. в блоге команды Помощника по Azure для пользователей SQL Server [здесь](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Следующая команда SqlPackage импортирует базу данных **AdventureWorks2017** из локального хранилища на сервер базы данных Azure S'L. Эта команда создает базу данных **myMigratedDatabase** с уровнем служб **Премиум** и целевым уровнем служб **P6**. Подставьте соответствующие значения для своей среды.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Чтобы подключиться к серверу Базы данных SQL, управляя отдельной базой данных при включенном корпоративном брандмауэре, необходимо открыть порт 1433 в брандмауэре. Чтобы подключиться к управляемому экземпляру, необходимо обладать [подключением типа "точка — сеть"](sql-database-managed-instance-configure-p2s.md) или подключением Express Route.

В этом примере показано, как импортировать базу данных с помощью SqlPackage, используя универсальную проверку подлинности Active Directory.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>Вопросы производительности

Скорость экспорта варьируется из-за многих факторов (например, форма данных), поэтому невозможно предсказать, какую скорость следует ожидать. SqlPackage может занять значительное время, особенно для больших баз данных.

Чтобы получить лучшую производительность вы можете попробовать следующие стратегии:

1. Убедитесь, что в базе данных не работает какая-либо другая рабочая нагрузка. Создание копии до экспорта может быть лучшим решением для обеспечения других рабочих нагрузок не работает.
2. Увеличьте цель уровня обслуживания баз данных (SLO), чтобы лучше справляться с экспортной рабочей нагрузкой (в первую очередь читать вв./о). Если база данных в настоящее время GP_Gen5_4, возможно, критический уровень для бизнеса поможет с работой по чтению.
3. Убедитесь, что существуют кластерные индексы, особенно для больших таблиц. 
4. Виртуальные машины (VM) должны находиться в том же регионе, что и база данных, чтобы избежать сетевых ограничений.
5. VMs должны иметь SSD с достаточным размером для генерации временных артефактов перед загрузкой в хранилище капли.
6. VMs должны иметь адекватную конфигурацию ядра и памяти для конкретной базы данных.

## <a name="store-the-imported-or-exported-bacpac-file"></a>Храните импортируемые или экспортируемые. Файл BACPAC

Teh. Файл BACPAC может храниться в [Azure Blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)или [Azure Files.](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) 

Для достижения наилучшей производительности используйте файлы Azure. SqlPackage работает с файловой системой, чтобы получить доступ к файлам Azure напрямую.

Чтобы снизить затраты, используйте Azure Blobs, которые стоят меньше, чем премиум-доля файлов Azure. Тем не менее, это потребует от вас скопировать [. Файл BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) между blob и локальной файловой системой перед операцией импорта или экспорта. В результате процесс займет больше времени.

Загрузить или загрузить . ФАЙЛы BACPAC, [см. Данные о передаче с помощью azCopy и Blob,](../storage/common/storage-use-azcopy-blobs.md)а также [передача данных с помощью AzCopy и хранения файлов.](../storage/common/storage-use-azcopy-files.md)

В зависимости от среды может потребоваться [настроить брандмауэры и виртуальные сети Azure Storage.](../storage/common/storage-network-security.md)

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать, как подключиться к импортируемой [Quickstart: Azure SQL Database: Use SQL Server Management Studio to connect and query data](sql-database-connect-query-ssms.md)базе данных S'L и задать запрос, см.
- Сведения о миграции из SQL Server в Базу данных SQL Azure с использованием BACPAC-файлов см. в [блоге группы консультирования клиентов SQL Server](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Описание процесса миграции базы данных SQL Server в базу данных SQL Azure, включая рекомендации по его использованию, см. в [этой статье](sql-database-single-database-migrate.md).
- Чтобы узнать, как безопасно управлять ключами к хранилищу данных и подписанными URL-адресами и совместно их использовать, ознакомьтесь с [руководством по безопасности службы хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
