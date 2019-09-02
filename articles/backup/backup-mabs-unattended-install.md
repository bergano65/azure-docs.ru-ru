---
title: Автоматическая установка Azure Backup Server версии 2
description: Использование скрипта PowerShell для автоматической установки Azure Backup Server версии 2. Этот тип установки также называется "тихой" установкой.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: dacurwin
ms.openlocfilehash: 3777aecea5e25b33a7010ad90887829406e491ae
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210167"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Запуск автоматической установки Azure Backup Server

Узнайте, как запустить автоматическую установку Azure Backup Server.

Эти действия не применимы, если выполняется установка Azure Backup Server версии 1.

## <a name="install-backup-server"></a>Установка Backup Server

1. На сервере, где будет использоваться Azure Backup Server версии 2 или более поздней, создайте текстовый файл. (Файл можно создать в блокноте или в другом текстовом редакторе.) Сохраните файл как MABSSetup.ini.

2. Вставьте следующий код в файл MABSSetup.ini. Замените текст в скобках (\< \>) значениями из среды. Ниже приведен пример текста.

   ```text
   [OPTIONS]
   UserName=administrator
   CompanyName=<Microsoft Corporation>
   SQLMachineName=localhost
   SQLInstanceName=<SQL instance name>
   SQLMachineUserName=administrator
   SQLMachinePassword=<admin password>
   SQLMachineDomainName=<machine domain>
   ReportingMachineName=localhost
   ReportingInstanceName=<reporting instance name>
   SqlAccountPassword=<admin password>
   ReportingMachineUserName=<username>
   ReportingMachinePassword=<reporting admin password>
   ReportingMachineDomainName=<domain>
   VaultCredentialFilePath=<vault credential full path and complete name>
   SecurityPassphrase=<passphrase>
   PassphraseSaveLocation=<passphrase save location>
   UseExistingSQL=<1/0 use or do not use existing SQL>
   ```

3. Сохраните файл. Затем в командной строке с повышенными привилегиями на сервере установки введите следующую команду:

   ```cmd
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

Для установки можно использовать следующие флаги:</br>
**/f** — путь к файлу INI.</br>
**/l** — путь к журналу.</br>
**/i** — путь установки.</br>
**/x** — путь для удаления.</br>

## <a name="next-steps"></a>Следующие шаги
После установки Backup Server узнайте, как подготовить сервер или обеспечить защиту рабочей нагрузки.

- [Подготовка к резервному копированию рабочих нагрузок с использованием Azure Backup Server](backup-azure-microsoft-azure-backup.md)
- [Резервное копирование сервера VMware в Azure](backup-azure-backup-server-vmware.md)
- [Архивация баз данных SQL Server в Azure с помощью Azure Backup Server](backup-azure-sql-mabs.md)
- [Добавление хранилища на Azure Backup Server версии 2](backup-mabs-add-storage.md)
