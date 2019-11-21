---
title: Заметки о выпуске агента службы "Синхронизация файлов Azure" | Документация Майкрософт
description: Заметки о выпуске агента службы "Синхронизация файлов Azure".
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 10/8/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 8328170d73d75e8e2eb1a84881375ce21c72a61b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227834"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Заметки о выпуске агента службы "Синхронизация файлов Azure"
Служба "Синхронизация файлов Azure" позволяет централизованно хранить файловые ресурсы организации в службе файлов Azure, обеспечивая гибкость, производительность и совместимость локального файлового сервера. Установки серверов Windows Server преобразовываются в быстрый кэш общего файлового ресурса Azure. Для локального доступа к данным вы можете использовать любой протокол, доступный в Windows Server (в том числе SMB, NFS и FTPS). Кроме того, вы можете создать любое количество кэшей.

В этой статье рассматриваются заметки о выпуске для поддерживаемых версий агента службы "Синхронизация файлов Azure".

## <a name="supported-versions"></a>Поддерживаемые версии
Служба "Синхронизация файлов Azure" поддерживает следующие версии агента:

| Веха | Номер версии агента | Дата выпуска | Status |
|----|----------------------|--------------|------------------|
| V8 Release - [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | October 8, 2019 | Поддерживается |
| July 2019 update rollup - [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 24 июля 2019 г. | Поддерживается |
| July 2019 update rollup - [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | July 12, 2019 | Поддерживается |
| V7 Release - [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | June 19, 2019 | Поддерживается |
| June 2019 update rollup - [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | June 27, 2019 | Поддерживается |
| June 2019 update rollup - [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | June 13, 2019 | Поддерживается |
| May 2019 update rollup - [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | May 7, 2019 | Поддерживается |
| V6 Release - [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | April 21, 2019 | Поддерживается |
| April 2019 update rollup - [KB4481061](https://support.microsoft.com/help/4481061)| 5.2.0.0 | April 4, 2019 | Поддерживается |
| March 2019 update rollup - [KB4481060](https://support.microsoft.com/help/4481060)| 5.1.0.0 | March 7, 2019 | Поддерживается |
| Выпуск версии 5 — [KB4459989](https://support.microsoft.com/help/4459989)| 5.0.2.0 | 12 февраля 2019 г. | Поддерживается |
| Выпуск версии 4 | 4.0.1.0 - 4.3.0.0 | Н/Д | Not Supported - Agent versions expired on November 6, 2019 |
| V3 Release | 3.1.0.0 - 3.4.0.0 | Н/Д | Not Supported - Agent versions expired on August 19, 2019 |
| Pre-GA agents | 1.1.0.0 — 3.0.13.0 | Н/Д | Не поддерживается. Версии агента истекли 1 октября 2018 г. |

### <a name="azure-file-sync-agent-update-policy"></a>Политика обновления агента службы синхронизации файлов Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-8000"></a>Agent version 8.0.0.0
The following release notes are for version 8.0.0.0 of the Azure File Sync agent (released October 8, 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Улучшения и решенные проблемы

- Restore performance Improvements
    - Faster recovery times for recovery done through Azure Backup. Restored files will sync back down to Azure File Sync servers much faster. 
- Improved cloud tiering portal experience  
    - If you have tiered files that are failing to recall, you can now view the recall errors in the server endpoint properties. Also, the server endpoint health will now show an error and mitigation steps if the cloud tiering filter driver is not loaded on the server.
- Simpler agent installation
    - The Az\AzureRM PowerShell module is no longer required to register the server making installation simpler and fast.
- Miscellaneous performance and reliability improvements

### <a name="evaluation-tool"></a>Средство оценки
Прежде чем развертывать службу "Синхронизация файлов Azure", следует оценить, совместима ли она с вашей системой, с помощью средства оценки этой службы. Это средство является командлетом Azure PowerShell, который проверяет наличие потенциальных проблем с файловой системой и набором данных, таких как неподдерживаемые символы или неподдерживаемая версия операционной системы. Инструкции по установке и использованию см. в разделе о [средстве оценки](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) руководства по планированию. 

### <a name="agent-installation-and-server-configuration"></a>Установка агента и конфигурация сервера
Дополнительные сведения о том, как установить и настроить агент службы "Синхронизация файлов Azure" с помощью Windows Server, см. в статье о [планировании развертывания службы "Синхронизация файлов Azure"](storage-sync-files-planning.md) и [практическом руководстве по развертыванию службы "Синхронизация файлов Azure"](storage-sync-files-deployment-guide.md).

- Пакет установки агента нужно устанавливать с разрешениями более высокого уровня (администратора).
- The agent is not supported on Nano Server deployment option.
- Агент поддерживается только в Windows Server 2019 и Windows Server 2016 и Windows Server 2012 R2.
- Агент требует по крайней мере 2 ГиБ памяти. Если сервер выполняется на виртуальной машине с включенной динамической памятью, в виртуальной машине должна быть настроена память объемом не менее 2048 МиБ.
- Служба агента синхронизации хранилища (FileSyncSvc) не поддерживает конечные точки сервера, которые размещены в томе со сжатым каталогом данных о системном томе (SVI). Использование этой конфигурации может привести к непредвиденным результатам.

### <a name="interoperability"></a>Возможность взаимодействия
- Антивирусные программы, приложения архивации, а также другие приложения, которые получают доступ к многоуровневым файлам, вызывают нежелательные отзывы, если они не учитывают автономный атрибут и пропускают чтение содержимого этих файлов. Дополнительные сведения см. в статье об [устранении неполадок службы "Синхронизация файлов Azure"](storage-sync-files-troubleshoot.md).
- Фильтры блокировки файлов FSRM могут стать причиной постоянных сбоев синхронизации.
- Running sysprep on a server that has the Azure File Sync agent installed is not supported and can lead to unexpected results. Устанавливать агент Синхронизации файлов Azure нужно после развертывания образа сервера и завершения мини-установки sysprep.

### <a name="sync-limitations"></a>Ограничения синхронизации
Следующие элементы не синхронизируются, но остальная часть системы продолжает работать в обычном режиме:
- Файлы с неподдерживаемыми символами. Со списком неподдерживаемых символов можно ознакомиться в [руководстве по устранению неполадок](storage-sync-files-troubleshoot.md#handling-unsupported-characters).
- Файлы или каталоги, которые оканчиваются точкой.
- Пути длиной более 2048 символов.
- Часть списка управления доступом на уровне пользователей (DACL) в дескрипторе безопасности размером более 2 КБ. (Эта проблема возникает только при наличии более 40 записей контроля доступа (ACE) на одном элементе.)
- Часть системного списка управления доступом (SACL) в дескрипторе безопасности, который используется для аудита.
- Дополнительные атрибуты.
- Альтернативные потоки данных.
- Точки повторного анализа.
- Жесткие связи.
- Сжатие (для серверного файла) не сохраняется при синхронизации изменений в этом файле из других конечных точек.
- Файлы, зашифрованные с помощью файловой системы EFS (или другого пользовательского режима шифрования). Служба не может считывать такие данные.

    > [!Note]  
    > Служба "Синхронизация файлов Azure" всегда шифрует передаваемые данные. Неактивные данные можно зашифровать в Azure.
 
### <a name="server-endpoint"></a>Конечная точка сервера
- Конечную точку сервера можно создавать только на томе NTFS. Сейчас служба "Синхронизация файлов Azure" не поддерживает ReFS и FAT, FAT32 и другие файловые системы.
- Многоуровневые файлы станут недоступными для использования, если их не отозвать перед удалением конечной точки сервера. Чтобы восстановить доступ к файлам, повторно создайте конечную точку сервера. Если с момента удаления конечной точки сервера прошло 30 дней или если была удалена конечная точка облака, многоуровневые файлы, которые не были отозваны, станут непригодными для использования. To learn more, see [Tiered files are not accessible on the server after deleting a server endpoint](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- Но для системного тома не поддерживается распределение по уровням облака. Чтобы создать конечную точку сервера на системном томе, отключите распределение по уровням облака при ее создании.
- Отказоустойчивую кластеризацию поддерживают только кластеризованные диски. Общие тома кластера (CSV) ее не поддерживают.
- Конечная точка сервера не может быть вложенной. Она может сосуществовать параллельно с другой конечной точкой на одном томе.
- Не храните файл ОС или файл подкачки приложения в расположении конечной точки сервера.
- В случае переименования сервера его имя на портале не обновляется.

### <a name="cloud-endpoint"></a>Облачная конечная точка
- Служба "Синхронизация файлов Azure" поддерживает внесение изменений непосредственно в файловый ресурс Azure. Однако внесенные изменения сначала должны быть обнаружены заданием обнаружения изменений этой службы. Задание обнаружения изменений инициируется для облачной конечной точки каждые 24 часа. To immediately sync files that are changed in the Azure file share, the [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet can be used to manually initiate the detection of changes in the Azure file share. Кроме того, изменения, внесенные в файловый ресурс Azure с помощью протокола REST, не приведут к обновлению времени последнего изменения SMB и не будут распознаны как изменение службой синхронизации.
- Службу синхронизации хранилища и/или учетную запись хранения можно переместить в другую группу ресурсов или подписку в рамках существующего клиента Azure AD. Если учетная запись хранения перемещена, необходимо предоставить службе Hybrid File Sync доступ к учетной записи хранилища (см. в разделе [Убедитесь, что служба "Синхронизация файлов Azure" имеет доступ к учетной записи хранения](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Служба "Синхронизация файлов Azure" не поддерживает перемещение подписки в другой клиент Azure AD.

### <a name="cloud-tiering"></a>Распределение по уровням облака
- Если многоуровневый файл копируется в другое расположение с помощью Robocopy, полученный файл не будет распределен по уровням. Вы можете задать автономный атрибут, так как Robocopy неправильно включает его в операциях копирования.
- При копировании файлов с помощью robocopy используйте параметр /MIR, чтобы сохранить метки времени файлов. Таким образом, старые файлы будут перемещены на другой уровень раньше, чем файлы, доступ к которым был получен недавно.

## <a name="agent-version-7200"></a>Agent version 7.2.0.0
The following release notes are for version 7.2.0.0 of the Azure File Sync agent released July 24, 2019. These notes are in addition to the release notes listed for version 7.0.0.0.

Список исправлений в этом выпуске:  
- Storage Sync Agent (FileSyncSvc) crashes if the proxy configuration is null.
- Server endpoint will start BCDR (error 0x80c80257 - ECS_E_BCDR_IN_PROGRESS) if multiple endpoints on the server have the same name.
- Cloud tiering reliability improvements.

## <a name="agent-version-7100"></a>Agent version 7.1.0.0
The following release notes are for version 7.1.0.0 of the Azure File Sync agent released July 12, 2019. These notes are in addition to the release notes listed for version 7.0.0.0.

Список исправлений в этом выпуске:  
- Accessing or browsing a server endpoint location over SMB is slow on Windows Server 2012 R2. 
- Increased CPU utilization after installing the Azure File Sync v6 agent.
- Cloud tiering telemetry improvements.
- Прочие улучшения надежности при распределении по уровням в облаке и синхронизации.

## <a name="agent-version-7000"></a>Agent version 7.0.0.0
The following release notes are for version 7.0.0.0 of the Azure File Sync agent (released June 19, 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Улучшения и решенные проблемы

- Support for larger file share sizes
    - With the preview of larger Azure file shares, we are increasing our support limits for file sync as well. In this first step, Azure File Sync now supports up to 25 TB and 50 million files in a single, syncing namespace. To apply for the large file share preview, fill in this form https://aka.ms/azurefilesatscalesurvey. 
- Support for firewall and virtual network setting on storage accounts
    - Azure File Sync now supports the firewall and virtual network setting on storage accounts. To configure your deployment to work with the firewall and virtual network setting, see [Configure firewall and virtual network settings](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).
- PowerShell cmdlet to immediately sync files changed in the Azure file share
    - To immediately sync files that are changed in the Azure file share, the Invoke-AzStorageSyncChangeDetection PowerShell cmdlet can be used to manually initiate the detection of changes in the Azure file share. This cmdlet is intended for scenarios where some type of automated process is making changes in the Azure file share or the changes are done by an administrator (like moving files and directories into the share). For end-user changes, the recommendation is to install the Azure File Sync agent in an IaaS VM and have end users access the file share through the IaaS VM. This way all changes will quickly sync to other agents without the need to use the Invoke-AzStorageSyncChangeDetection cmdlet. To learn more, see the [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) documentation.
- Improved portal experience if you encounter files that are not syncing
    - If you have files that are failing to sync, we now differentiate between transient and persistent errors in the portal. Transient errors usually resolve themselves without the need for admin action. For example, a file that is currently in use will not sync until the file handle is closed. For persistent errors, we now show the number of files impacted by each error. The persistent error count is also displayed in the files not syncing column of all server endpoints in a sync group.
- Improved Azure Backup file-level restore
    - Individual files restored using Azure Backup are now detected and synced to the server endpoint faster.
- Improved cloud tiering recall cmdlet reliability 
    - The Invoke-StorageSyncFileRecall cmdlet now allows customers to specify per file retry count and per file retry delay similar to robocopy. Previously, this cmdlet would recall all tiered files under a given path in random order. With the new -Order parameter, this cmdlet will recall the hottest data first and honor the cloud tiering policy (stop recalling if the date policy is met or the volume free space is met; whichever happens first).
- Support for TLS 1.2 only (TLS 1.0 and 1.1 is disabled)
    - Azure File Sync now supports using TLS 1.2 only on servers that have TLS 1.0 and 1.1 disabled. Prior to this improvement, server registration would fail if TLS 1.0 and 1.1 was disabled on the server.
- Miscellaneous performance and reliability improvements for sync and cloud tiering
    - There are several reliability and performance improvements in this release. Some of them are targeted to make cloud tiering more efficient and Azure File Sync as a whole work better in those situations when you have a bandwidth throttling schedule set.

### <a name="evaluation-tool"></a>Средство оценки
Прежде чем развертывать службу "Синхронизация файлов Azure", следует оценить, совместима ли она с вашей системой, с помощью средства оценки этой службы. Это средство является командлетом Azure PowerShell, который проверяет наличие потенциальных проблем с файловой системой и набором данных, таких как неподдерживаемые символы или неподдерживаемая версия операционной системы. Инструкции по установке и использованию см. в разделе о [средстве оценки](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) руководства по планированию. 

### <a name="agent-installation-and-server-configuration"></a>Установка агента и конфигурация сервера
Дополнительные сведения о том, как установить и настроить агент службы "Синхронизация файлов Azure" с помощью Windows Server, см. в статье о [планировании развертывания службы "Синхронизация файлов Azure"](storage-sync-files-planning.md) и [практическом руководстве по развертыванию службы "Синхронизация файлов Azure"](storage-sync-files-deployment-guide.md).

- Пакет установки агента нужно устанавливать с разрешениями более высокого уровня (администратора).
- The agent is not supported on Nano Server deployment option.
- Агент поддерживается только в Windows Server 2019 и Windows Server 2016 и Windows Server 2012 R2.
- Агент требует по крайней мере 2 ГиБ памяти. Если сервер выполняется на виртуальной машине с включенной динамической памятью, в виртуальной машине должна быть настроена память объемом не менее 2048 МиБ.
- Служба агента синхронизации хранилища (FileSyncSvc) не поддерживает конечные точки сервера, которые размещены в томе со сжатым каталогом данных о системном томе (SVI). Использование этой конфигурации может привести к непредвиденным результатам.

### <a name="interoperability"></a>Возможность взаимодействия
- Антивирусные программы, приложения архивации, а также другие приложения, которые получают доступ к многоуровневым файлам, вызывают нежелательные отзывы, если они не учитывают автономный атрибут и пропускают чтение содержимого этих файлов. Дополнительные сведения см. в статье об [устранении неполадок службы "Синхронизация файлов Azure"](storage-sync-files-troubleshoot.md).
- Фильтры блокировки файлов FSRM могут стать причиной постоянных сбоев синхронизации.
- Running sysprep on a server that has the Azure File Sync agent installed is not supported and can lead to unexpected results. Устанавливать агент Синхронизации файлов Azure нужно после развертывания образа сервера и завершения мини-установки sysprep.

### <a name="sync-limitations"></a>Ограничения синхронизации
Следующие элементы не синхронизируются, но остальная часть системы продолжает работать в обычном режиме:
- Файлы с неподдерживаемыми символами. Со списком неподдерживаемых символов можно ознакомиться в [руководстве по устранению неполадок](storage-sync-files-troubleshoot.md#handling-unsupported-characters).
- Файлы или каталоги, которые оканчиваются точкой.
- Пути длиной более 2048 символов.
- Часть списка управления доступом на уровне пользователей (DACL) в дескрипторе безопасности размером более 2 КБ. (Эта проблема возникает только при наличии более 40 записей контроля доступа (ACE) на одном элементе.)
- Часть системного списка управления доступом (SACL) в дескрипторе безопасности, который используется для аудита.
- Дополнительные атрибуты.
- Альтернативные потоки данных.
- Точки повторного анализа.
- Жесткие связи.
- Сжатие (для серверного файла) не сохраняется при синхронизации изменений в этом файле из других конечных точек.
- Файлы, зашифрованные с помощью файловой системы EFS (или другого пользовательского режима шифрования). Служба не может считывать такие данные.

    > [!Note]  
    > Служба "Синхронизация файлов Azure" всегда шифрует передаваемые данные. Неактивные данные можно зашифровать в Azure.
 
### <a name="server-endpoint"></a>Конечная точка сервера
- Конечную точку сервера можно создавать только на томе NTFS. Сейчас служба "Синхронизация файлов Azure" не поддерживает ReFS и FAT, FAT32 и другие файловые системы.
- Многоуровневые файлы станут недоступными для использования, если их не отозвать перед удалением конечной точки сервера. Чтобы восстановить доступ к файлам, повторно создайте конечную точку сервера. Если с момента удаления конечной точки сервера прошло 30 дней или если была удалена конечная точка облака, многоуровневые файлы, которые не были отозваны, станут непригодными для использования.
- Но для системного тома не поддерживается распределение по уровням облака. Чтобы создать конечную точку сервера на системном томе, отключите распределение по уровням облака при ее создании.
- Отказоустойчивую кластеризацию поддерживают только кластеризованные диски. Общие тома кластера (CSV) ее не поддерживают.
- Конечная точка сервера не может быть вложенной. Она может сосуществовать параллельно с другой конечной точкой на одном томе.
- Не храните файл ОС или файл подкачки приложения в расположении конечной точки сервера.
- В случае переименования сервера его имя на портале не обновляется.

### <a name="cloud-endpoint"></a>Облачная конечная точка
- Служба "Синхронизация файлов Azure" поддерживает внесение изменений непосредственно в файловый ресурс Azure. Однако внесенные изменения сначала должны быть обнаружены заданием обнаружения изменений этой службы. Задание обнаружения изменений инициируется для облачной конечной точки каждые 24 часа. Кроме того, изменения, внесенные в файловый ресурс Azure с помощью протокола REST, не приведут к обновлению времени последнего изменения SMB и не будут распознаны как изменение службой синхронизации.
- Службу синхронизации хранилища и/или учетную запись хранения можно переместить в другую группу ресурсов или подписку в рамках существующего клиента Azure AD. Если учетная запись хранения перемещена, необходимо предоставить службе Hybrid File Sync доступ к учетной записи хранилища (см. в разделе [Убедитесь, что служба "Синхронизация файлов Azure" имеет доступ к учетной записи хранения](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Служба "Синхронизация файлов Azure" не поддерживает перемещение подписки в другой клиент Azure AD.

### <a name="cloud-tiering"></a>Распределение по уровням облака
- Если многоуровневый файл копируется в другое расположение с помощью Robocopy, полученный файл не будет распределен по уровням. Вы можете задать автономный атрибут, так как Robocopy неправильно включает его в операциях копирования.
- При копировании файлов с помощью robocopy используйте параметр /MIR, чтобы сохранить метки времени файлов. Таким образом, старые файлы будут перемещены на другой уровень раньше, чем файлы, доступ к которым был получен недавно.

## <a name="agent-version-6300"></a>Agent version 6.3.0.0
The following release notes are for version 6.3.0.0 of the Azure File Sync agent released June 27, 2019. These notes are in addition to the release notes listed for version 6.0.0.0.

Список исправлений в этом выпуске:  
- Accessing or browsing a server endpoint location over SMB is slow on Windows Server 2012 R2 
- Increased CPU utilization after installing the Azure File Sync v6 agent
- Cloud tiering telemetry improvements

## <a name="agent-version-6200"></a>Agent version 6.2.0.0
The following release notes are for version 6.2.0.0 of the Azure File Sync agent released June 13, 2019. These notes are in addition to the release notes listed for version 6.0.0.0.

Список исправлений в этом выпуске:  
- After creating a server endpoint, High CPU usage may occur when background recall is downloading files to the server
- Sync and cloud tiering operations may fail with error ECS_E_SERVER_CREDENTIAL_NEEDED due to token expiration
- Recalling a file may fail if the URL to download the file contains reserved characters 

## <a name="agent-version-6100"></a>Agent version 6.1.0.0
The following release notes are for version 6.1.0.0 of the Azure File Sync agent released May 6, 2019. These notes are in addition to the release notes listed for version 6.0.0.0.

Список исправлений в этом выпуске:  
- Windows Admin Center fails to display the agent version and server endpoint configuration on servers which have Azure File Sync agent version 6.0 installed.

## <a name="agent-version-6000"></a>Agent version 6.0.0.0
The following release notes are for version 6.0.0.0 of the Azure File Sync agent (released April 22, 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Улучшения и решенные проблемы

- Agent auto-update support
  - We have heard your feedback and added an auto-update feature into the Azure File Sync server agent. For more information, see [Azure File Sync agent update policy](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Support for Azure file share ACLs
  - Azure File Sync has always supported syncing ACLs between server endpoints but the ACLs were not synced to the cloud endpoint (Azure file share). This release adds support for syncing ACLs between server and cloud endpoints.
- Parallel upload and download sync sessions for a server endpoint 
  - Server endpoints now support uploading and downloading files at the same time. No more waiting for a download to complete so files can be uploaded to the Azure file share. 
- New Cloud Tiering cmdlets to get volume and tiering status
  - Two new, server-local PowerShell cmdlets can now be used to obtain cloud tiering and file recall information. They make logging information from two event channels on the server available:
    - Get-StorageSyncFileTieringResult will list all files and their paths that haven't tiered and reports on the reason why.
    - Get-StorageSyncFileRecallResult reports all file recall events. It lists every file recalled and its path as well as success or error for that recall.
  - By default, both event channels can store up to 1 MB each – you can increase the amount of files reported by increasing the event channel size.
- Support for FIPS mode
  - Azure File Sync now supports enabling FIPS mode on servers which have the Azure File Sync agent installed.
    - Prior to enabling FIPS mode on your server, install the Azure File Sync agent and [PackageManagement module](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) on your server. If FIPS is already enabled on the server, [manually download](/powershell/scripting/gallery/how-to/working-with-packages/manual-download) the [PackageManagement module](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) to your server.
- Miscellaneous reliability improvements for cloud tiering and sync

### <a name="evaluation-tool"></a>Средство оценки
Прежде чем развертывать службу "Синхронизация файлов Azure", следует оценить, совместима ли она с вашей системой, с помощью средства оценки этой службы. Это средство является командлетом Azure PowerShell, который проверяет наличие потенциальных проблем с файловой системой и набором данных, таких как неподдерживаемые символы или неподдерживаемая версия операционной системы. Инструкции по установке и использованию см. в разделе о [средстве оценки](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) руководства по планированию. 

### <a name="agent-installation-and-server-configuration"></a>Установка агента и конфигурация сервера
Дополнительные сведения о том, как установить и настроить агент службы "Синхронизация файлов Azure" с помощью Windows Server, см. в статье о [планировании развертывания службы "Синхронизация файлов Azure"](storage-sync-files-planning.md) и [практическом руководстве по развертыванию службы "Синхронизация файлов Azure"](storage-sync-files-deployment-guide.md).

- Пакет установки агента нужно устанавливать с разрешениями более высокого уровня (администратора).
- The agent is not supported on Nano Server deployment option.
- Агент поддерживается только в Windows Server 2019 и Windows Server 2016 и Windows Server 2012 R2.
- Агент требует по крайней мере 2 ГиБ памяти. Если сервер выполняется на виртуальной машине с включенной динамической памятью, в виртуальной машине должна быть настроена память объемом не менее 2048 МиБ.
- Служба агента синхронизации хранилища (FileSyncSvc) не поддерживает конечные точки сервера, которые размещены в томе со сжатым каталогом данных о системном томе (SVI). Использование этой конфигурации может привести к непредвиденным результатам.

### <a name="interoperability"></a>Возможность взаимодействия
- Антивирусные программы, приложения архивации, а также другие приложения, которые получают доступ к многоуровневым файлам, вызывают нежелательные отзывы, если они не учитывают автономный атрибут и пропускают чтение содержимого этих файлов. Дополнительные сведения см. в статье об [устранении неполадок службы "Синхронизация файлов Azure"](storage-sync-files-troubleshoot.md).
- Фильтры блокировки файлов FSRM могут стать причиной постоянных сбоев синхронизации.
- Запуск sysprep на сервере, где установлен агент службы "Синхронизация файлов Azure", не поддерживается и может привести к непредвиденным результатам. Устанавливать агент Синхронизации файлов Azure нужно после развертывания образа сервера и завершения мини-установки sysprep.

### <a name="sync-limitations"></a>Ограничения синхронизации
Следующие элементы не синхронизируются, но остальная часть системы продолжает работать в обычном режиме:
- Файлы с неподдерживаемыми символами. Со списком неподдерживаемых символов можно ознакомиться в [руководстве по устранению неполадок](storage-sync-files-troubleshoot.md#handling-unsupported-characters).
- Файлы или каталоги, которые оканчиваются точкой.
- Пути длиной более 2048 символов.
- Часть списка управления доступом на уровне пользователей (DACL) в дескрипторе безопасности размером более 2 КБ. (Эта проблема возникает только при наличии более 40 записей контроля доступа (ACE) на одном элементе.)
- Часть системного списка управления доступом (SACL) в дескрипторе безопасности, который используется для аудита.
- Дополнительные атрибуты.
- Альтернативные потоки данных.
- Точки повторного анализа.
- Жесткие связи.
- Сжатие (для серверного файла) не сохраняется при синхронизации изменений в этом файле из других конечных точек.
- Файлы, зашифрованные с помощью файловой системы EFS (или другого пользовательского режима шифрования). Служба не может считывать такие данные.

    > [!Note]  
    > Служба "Синхронизация файлов Azure" всегда шифрует передаваемые данные. Неактивные данные можно зашифровать в Azure.
 
### <a name="server-endpoint"></a>Конечная точка сервера
- Конечную точку сервера можно создавать только на томе NTFS. Сейчас служба "Синхронизация файлов Azure" не поддерживает ReFS и FAT, FAT32 и другие файловые системы.
- Многоуровневые файлы станут недоступными для использования, если их не отозвать перед удалением конечной точки сервера. Чтобы восстановить доступ к файлам, повторно создайте конечную точку сервера. Если с момента удаления конечной точки сервера прошло 30 дней или если была удалена конечная точка облака, многоуровневые файлы, которые не были отозваны, станут непригодными для использования.
- Но для системного тома не поддерживается распределение по уровням облака. Чтобы создать конечную точку сервера на системном томе, отключите распределение по уровням облака при ее создании.
- Отказоустойчивую кластеризацию поддерживают только кластеризованные диски. Общие тома кластера (CSV) ее не поддерживают.
- Конечная точка сервера не может быть вложенной. Она может сосуществовать параллельно с другой конечной точкой на одном томе.
- Не храните файл ОС или файл подкачки приложения в расположении конечной точки сервера.
- В случае переименования сервера его имя на портале не обновляется.

### <a name="cloud-endpoint"></a>Облачная конечная точка
- Служба "Синхронизация файлов Azure" поддерживает внесение изменений непосредственно в файловый ресурс Azure. Однако внесенные изменения сначала должны быть обнаружены заданием обнаружения изменений этой службы. Задание обнаружения изменений инициируется для облачной конечной точки каждые 24 часа. Кроме того, изменения, внесенные в файловый ресурс Azure с помощью протокола REST, не приведут к обновлению времени последнего изменения SMB и не будут распознаны как изменение службой синхронизации.
- Службу синхронизации хранилища и/или учетную запись хранения можно переместить в другую группу ресурсов или подписку в рамках существующего клиента Azure AD. Если учетная запись хранения перемещена, необходимо предоставить службе Hybrid File Sync доступ к учетной записи хранилища (см. в разделе [Убедитесь, что служба "Синхронизация файлов Azure" имеет доступ к учетной записи хранения](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Служба "Синхронизация файлов Azure" не поддерживает перемещение подписки в другой клиент Azure AD.

### <a name="cloud-tiering"></a>Распределение по уровням облака
- Если многоуровневый файл копируется в другое расположение с помощью Robocopy, полученный файл не будет распределен по уровням. Вы можете задать автономный атрибут, так как Robocopy неправильно включает его в операциях копирования.
- При копировании файлов с помощью robocopy используйте параметр /MIR, чтобы сохранить метки времени файлов. Таким образом, старые файлы будут перемещены на другой уровень раньше, чем файлы, доступ к которым был получен недавно.
- При просмотре свойств файла из клиента SMB автономный атрибут может быть настроен неправильно из-за кэширования метаданных файла по протоколу SMB.

## <a name="agent-version-5200"></a>Agent version 5.2.0.0
The following release notes are for version 5.2.0.0 of the Azure File Sync agent released April 4, 2019. These notes are in addition to the release notes listed for version 5.0.2.0.

Список исправлений в этом выпуске:  
- Reliability improvements for offline data transfer and data transfer resume features
- Sync telemetry improvements

## <a name="agent-version-5100"></a>Agent version 5.1.0.0
The following release notes are for version 5.1.0.0 of the Azure File Sync agent released March 7, 2019. These notes are in addition to the release notes listed for version 5.0.2.0.

Список исправлений в этом выпуске:  
- Files may fail to sync with error 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) if change enumeration is failing on the server
- If a sync session or file receives an error 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE), sync will now retry the operation
- Files may fail to sync with error 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- High memory usage may occur when recalling files
- Cloud tiering telemetry improvements 

## <a name="agent-version-5020"></a>Версия агента 5.0.2.0
Следующие заметки о выпуске предназначены для версии агента службы "Синхронизация файлов Azure" 5.0.2.0 (выпущенной 12 февраля 2019 г).

### <a name="improvements-and-issues-that-are-fixed"></a>Улучшения и решенные проблемы

- Поддержка облака Azure для государственных организаций
  - Добавлена поддержка предварительной версии облака Azure для государственных организаций. Для работы с ней требуется разрешенная подписка и специальный агент, скачать который можно с веб-сайта Майкрософт. Чтобы получить доступ к предварительной версии, отправьте письмо на адрес [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com).
- Поддержка дедупликации данных
    - Дедупликация данных теперь полностью поддерживается с включенным распределением по уровням в облаке в Windows Server 2016 и Windows Server 2019. Включив дедупликацию в томе с включенным распределением по уровням в облаке, вы можете кэшировать больше файлов локально без выделения дополнительного объема памяти.
- Поддержка передачи данных в автономном режиме (например, с помощью Data Box)
    - Без труда переносите большие объемы данных в службу "Синхронизация файлов" любым удобным способом. You can choose Azure Data Box, AzCopy and even third-party migration services. Для получения данных в Azure не требуется высокая пропускная способность — с помощью Data Box достаточно отправить данные по почте. См. дополнительные сведения в статье по [автономной передаче данных](https://aka.ms/AFS/OfflineDataTransfer).
- Повышена скорость синхронизации
    - В версиях, предшествующих этому выпуску, синхронизация для клиентов с несколькими конечными точками сервера в одном и том же томе может выполняться с задержками. Служба "Синхронизация файлов Azure" раз в день создает на сервере временный моментальный снимок VSS для синхронизации файлов с открытыми дескрипторами. Теперь синхронизация нескольких конечных точек в одном и том же томе поддерживается во время активного сеанса синхронизации VSS. Вам больше не нужно ждать завершения сеанса синхронизации VSS, чтобы возобновить синхронизацию на других конечных точках сервера в томе.
- Улучшен мониторинг на портале
    - Теперь на портале службы синхронизации хранилища можно просматривать диаграммы по следующим показателям:
        - количество синхронизированных файлов;
        - размер переданных данных;
        - количество несинхронизированных файлов;
        - размер отозванных данных;
        - состояние подключения к серверу.
    - См. дополнительные сведения в статье [Мониторинг Синхронизации файлов Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring).
- Улучшена масштабируемость и надежность
    - Максимальное число объектов файловой системы (папок и файлов) в каталоге увеличено до 1 000 000. Ранее максимальное количество ограничивалось 200 000.
    - При прерывании передачи больших файлов служба синхронизации попытается возобновить передачу данных вместо их повторной отправки. 

### <a name="evaluation-tool"></a>Средство оценки
Прежде чем развертывать службу "Синхронизация файлов Azure", следует оценить, совместима ли она с вашей системой, с помощью средства оценки этой службы. Это средство является командлетом Azure PowerShell, который проверяет наличие потенциальных проблем с файловой системой и набором данных, таких как неподдерживаемые символы или неподдерживаемая версия операционной системы. Инструкции по установке и использованию см. в разделе о [средстве оценки](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) руководства по планированию. 

### <a name="agent-installation-and-server-configuration"></a>Установка агента и конфигурация сервера
Дополнительные сведения о том, как установить и настроить агент службы "Синхронизация файлов Azure" с помощью Windows Server, см. в статье о [планировании развертывания службы "Синхронизация файлов Azure"](storage-sync-files-planning.md) и [практическом руководстве по развертыванию службы "Синхронизация файлов Azure"](storage-sync-files-deployment-guide.md).

- Пакет установки агента нужно устанавливать с разрешениями более высокого уровня (администратора).
- Агент не поддерживается в развертываниях Windows Server Core или Nano Server.
- Агент поддерживается только в Windows Server 2019 и Windows Server 2016 и Windows Server 2012 R2.
- Агент требует по крайней мере 2 ГиБ памяти. Если сервер выполняется на виртуальной машине с включенной динамической памятью, в виртуальной машине должна быть настроена память объемом не менее 2048 МиБ.
- Служба агента синхронизации хранилища (FileSyncSvc) не поддерживает конечные точки сервера, которые размещены в томе со сжатым каталогом данных о системном томе (SVI). Использование этой конфигурации может привести к непредвиденным результатам.
- Режим FIPS не поддерживается и должен быть отключен. 

### <a name="interoperability"></a>Возможность взаимодействия
- Антивирусные программы, приложения архивации, а также другие приложения, которые получают доступ к многоуровневым файлам, вызывают нежелательные отзывы, если они не учитывают автономный атрибут и пропускают чтение содержимого этих файлов. Дополнительные сведения см. в статье об [устранении неполадок службы "Синхронизация файлов Azure"](storage-sync-files-troubleshoot.md).
- Фильтры блокировки файлов FSRM могут стать причиной постоянных сбоев синхронизации.
- Запуск sysprep на сервере, где установлен агент службы "Синхронизация файлов Azure", не поддерживается и может привести к непредвиденным результатам. Устанавливать агент Синхронизации файлов Azure нужно после развертывания образа сервера и завершения мини-установки sysprep.

### <a name="sync-limitations"></a>Ограничения синхронизации
Следующие элементы не синхронизируются, но остальная часть системы продолжает работать в обычном режиме:
- Файлы с неподдерживаемыми символами. Со списком неподдерживаемых символов можно ознакомиться в [руководстве по устранению неполадок](storage-sync-files-troubleshoot.md#handling-unsupported-characters).
- Файлы или каталоги, которые оканчиваются точкой.
- Пути длиной более 2048 символов.
- Часть списка управления доступом на уровне пользователей (DACL) в дескрипторе безопасности размером более 2 КБ. (Эта проблема возникает только при наличии более 40 записей контроля доступа (ACE) на одном элементе.)
- Часть системного списка управления доступом (SACL) в дескрипторе безопасности, который используется для аудита.
- Дополнительные атрибуты.
- Альтернативные потоки данных.
- Точки повторного анализа.
- Жесткие связи.
- Сжатие (для серверного файла) не сохраняется при синхронизации изменений в этом файле из других конечных точек.
- Файлы, зашифрованные с помощью файловой системы EFS (или другого пользовательского режима шифрования). Служба не может считывать такие данные.

    > [!Note]  
    > Служба "Синхронизация файлов Azure" всегда шифрует передаваемые данные. Неактивные данные можно зашифровать в Azure.
 
### <a name="server-endpoint"></a>Конечная точка сервера
- Конечную точку сервера можно создавать только на томе NTFS. Сейчас служба "Синхронизация файлов Azure" не поддерживает ReFS и FAT, FAT32 и другие файловые системы.
- Многоуровневые файлы станут недоступными для использования, если их не отозвать перед удалением конечной точки сервера. Чтобы восстановить доступ к файлам, повторно создайте конечную точку сервера. Если с момента удаления конечной точки сервера прошло 30 дней или если была удалена конечная точка облака, многоуровневые файлы, которые не были отозваны, станут непригодными для использования.
- Но для системного тома не поддерживается распределение по уровням облака. Чтобы создать конечную точку сервера на системном томе, отключите распределение по уровням облака при ее создании.
- Отказоустойчивую кластеризацию поддерживают только кластеризованные диски. Общие тома кластера (CSV) ее не поддерживают.
- Конечная точка сервера не может быть вложенной. Она может сосуществовать параллельно с другой конечной точкой на одном томе.
- Не храните файл ОС или файл подкачки приложения в расположении конечной точки сервера.
- В случае переименования сервера его имя на портале не обновляется.

### <a name="cloud-endpoint"></a>Облачная конечная точка
- Служба "Синхронизация файлов Azure" поддерживает внесение изменений непосредственно в файловый ресурс Azure. Однако внесенные изменения сначала должны быть обнаружены заданием обнаружения изменений этой службы. Задание обнаружения изменений инициируется для облачной конечной точки каждые 24 часа. Кроме того, изменения, внесенные в файловый ресурс Azure с помощью протокола REST, не приведут к обновлению времени последнего изменения SMB и не будут распознаны как изменение службой синхронизации.
- Службу синхронизации хранилища и/или учетную запись хранения можно переместить в другую группу ресурсов или подписку в рамках существующего клиента Azure AD. Если учетная запись хранения перемещена, необходимо предоставить службе Hybrid File Sync доступ к учетной записи хранилища (см. в разделе [Убедитесь, что служба "Синхронизация файлов Azure" имеет доступ к учетной записи хранения](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Служба "Синхронизация файлов Azure" не поддерживает перемещение подписки в другой клиент Azure AD.

### <a name="cloud-tiering"></a>Распределение по уровням облака
- Если многоуровневый файл копируется в другое расположение с помощью Robocopy, полученный файл не будет распределен по уровням. Вы можете задать автономный атрибут, так как Robocopy неправильно включает его в операциях копирования.
- При копировании файлов с помощью robocopy используйте параметр /MIR, чтобы сохранить метки времени файлов. Таким образом, старые файлы будут перемещены на другой уровень раньше, чем файлы, доступ к которым был получен недавно.
- При просмотре свойств файла из клиента SMB автономный атрибут может быть настроен неправильно из-за кэширования метаданных файла по протоколу SMB.
