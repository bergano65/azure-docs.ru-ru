---
title: Заметки о выпуске Обозревателя службы хранилища Microsoft Azure
description: Заметки о выпуске Обозревателя службы хранилища Microsoft Azure.
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2018
ms.author: cawa
ms.openlocfilehash: 0b2ffc00b6c96f2c31a4b711f618e7b87b6f69e0
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482102"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Заметки о выпуске Обозревателя службы хранилища Microsoft Azure

This article contains the latest release notes for Azure Storage Explorer, as well as release notes for previous versions. 

[Обозреватель службы хранилища Microsoft Azure](./vs-azure-tools-storage-manage-with-storage-explorer.md) — это изолированное приложение, которое упрощает работу с данными из службы хранилища Azure на платформе Windows, macOS и Linux.

To download previous versions of Storage Explorer, you can visit the [Releases page](https://github.com/microsoft/AzureStorageExplorer/releases) of our GitHub repo.

## <a name="version-1110"></a>Version 1.11.0
11/4/2019

### <a name="new"></a>Новинка
* Operations for Blobs, ADLS Gen2 and Managed Disks use the integrated AzCopy. More specifically, the following operations are done using AzCopy:
   * Большие двоичные объекты
      * Open for editing + Upload
      * Upload, including drag & drop
      * Download (Скачать)
      * Copy & paste #1249
      * Удаление
   * ADLS Gen2 Blobs
      * Upload, including drag & drop
      * Download (Скачать)
      * Copy & paste
      * Delete, including folder delete
   * Управляемые диски
      * Отправка
      * Download (Скачать)
      * Copy & paste

   Additionally, several frequently requested features have been added to the integrated AzCopy experience:
   * Conflict resolutions - you will be prompted during transfers to resolve conflicts. #1455
   * Upload as page blobs - you can choose whether or not AzCopy uploads .vhd and .vhdx files as page blobs. #1164 and #1601
   * Configurable AzCopy parameters - Several settings have been added to tune AzCopy's performance and resource usage. See more details below.

* To enable ADLS Gen2 and Blobs multi-protocol access and further enhance ADLS Gen2 experiences, we have added the following features for the ADLS Gen2 accounts:
   * Search using friendly names to set ACL permissions
   * View hidden containers, such as $logs and $web
   * Acquire and break container lease
   * Acquire and break Blob lease #848
   * Manage container access policies
   * Configure Blob access tiers
   * Copy & Paste Blobs

* In this release, we are previewing 17 additional languages. You can switch to a language of your choice on the settings page under "Application" → "Regional Settings" → "Language (Preview)". We are still working hard on translating additional strings and improving the translation quality. Should you have any feedback regarding a translation, or if you notice a string which is not yet translated, please [open an issue on GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%F0%9F%8C%90%20localization&template=bug-report.md&title=).
* In every release, we try to onboard a few settings to enable fine turning Storage Explorer. In this release, we added settings to further configure AzCopy as well as to hide service nodes:
   * AzCopy bandwidth limit - helps control how much of the network AzCopy uses. You can find this setting at "Transfers" → "AzCopy" → "Maximum transfer rate". #1099
   * AzCopy MD5 check - lets you configure if and how strictly AzCopy checks for MD5 hashes on download. You can find this setting at "Transfers" → "AzCopy" → "Check MD5".
   * AzCopy concurrency and memory buffer size - by default AzCopy will analyze your machine to determine reasonable default values for these settings. But if you run into performance problems, these advanced settings can be used to further tailor how AzCopy runs on your computer. You can find these settings under "Transfers" → "AzCopy". #994
   * Display and hide service nodes - these settings give you the options to display or hide any of the Azure services that Storage Explorer supports. You can find these settings under the "Services" section. #1877

* When creating a Snapshot of a Managed Disk, a default name is now provided. #1847
* When attaching with Azure AD, if you attach an ADLS Gen2 Blob container, then "(ADLS Gen2)" will be shown next to the node. #1861

### <a name="fixes"></a>Исправления
* When copying, uploading, or downloading large Disks, Storage Explorer would sometimes fail to revoke access to the disks involved in the operation. Эта ошибка исправлена. #2048
* Table statistics failed when viewing a partition key query. Эта ошибка исправлена. #1886

### <a name="known-issues"></a>Известные проблемы
* Storage Explorer 1.11.0 now requires a DFS endpoint (such as "myaccount.dfs.core.windows.net") to attach to ADLS Gen2 containers. Previous versions of Storage Explorer allowed you to use a blob endpoint. These attachments may no longer work after upgrading to 1.11.0. If you encounter this problem, reattach using the DFS endpoint.
* Numeric settings are not checked for whether they lie in a valid range.#2140
* Copying blob containers from one storage account to another in the tree view may fail. We are investigating the issue.#2124
* The Auto Refresh setting does not yet affect all operations in the Blob Explorer.
* Managed Disk features are not supported in Azure Stack.
* If a Disk upload or paste fails and a new Disk was created prior to the failure, Storage Explorer will not delete the Disk for you.
* Depending on when you cancel a Disk upload or paste, it is possible to leave the new Disk in a corrupted state. If this happens, you either need to delete the new Disk, or manually call the Disk APIs to replace the contents of the Disk such that it is no longer corrupted.
* When using RBAC, Storage Explorer requires some management layer permissions in order to access your storage resources. See the [troubleshooting guide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) for more info.
* Отключение от ресурса, подключенного через URI SAS, например контейнера больших двоичных объектов, может привести к ошибке, которая предотвращает правильное отображение других подключенных объектов. Чтобы обойти эту проблему, просто обновите узел группы. Для получения дополнительных сведений ознакомьтесь с исправлением № 537.
* Если вы используете VS для Mac и когда-либо создавали пользовательскую конфигурацию AAD, вы не сможете выполнить вход. Чтобы обойти эту проблему, удалите содержимое в папке ~/.IdentityService/AadConfigurations. Если это не разблокирует вашу учетную запись, оставьте комментарий об этой проблеме.
* В Azurite еще не полностью реализованы все API-интерфейсы хранилища. Из-за этого могут возникнуть непредвиденные ошибки или поведение при использовании Azurite в хранилище разработки.
* В редких случаях фокус дерева мог задержаться на функции быстрого доступа. Разблокировать фокус можно командой "Обновить все".
* Загрузка из папки OneDrive не работает из-за ошибки в NodeJS. Ошибка была исправлена, но еще не интегрирована в Electron. Чтобы обойти эту проблему при передаче и скачивании данных с использованием контейнера больших двоичных объектов, можно использовать экспериментальную функцию AzCopy.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный здесь обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* В Azure Stack не поддерживаются приведенные ниже возможности. Попытка использовать их при работе с ресурсами Azure Stack может привести к непредвиденным ошибкам.
   * Общие папки
   * Уровни доступа
   * обратимое удаление.
   * ADLS Gen2
   * Управляемые диски
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Running Storage Explorer on Linux requires certain dependencies to be installed first. Check the Storage Explorer [troubleshooting guide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) for more information.

## <a name="previous-releases"></a>Предыдущие выпуски

* [Version 1.10.1](#version-1101)
* [Version 1.10.0](#version-1100)
* [Version 1.9.0](#version-190)
* [Version 1.8.1](#version-181)
* [Version 1.8.0](#version-180)
* [Version 1.7.0](#version-170)
* [Version 1.6.2](#version-162)
* [Версия 1.6.1](#version-161)
* [Версия 1.6.0](#version-160)
* [Версия 1.5.0](#version-150)
* [Версия 1.4.4](#version-144)
* [Версия 1.4.3](#version-143)
* [Версия 1.4.2](#version-142)
* [Версия 1.4.1](#version-141)
* [Версия 1.3.0](#version-130)
* [Версия 1.2.0](#version-120)
* [Версия 1.1.0](#version-110)
* [Версия 1.0.0](#version-100)
* [Версия 0.9.6](#version-096)
* [Версия 0.9.5](#version-095)
* [Версии 0.9.4 и 0.9.3](#version-094-and-093)
* [Версия 0.9.2](#version-092)
* [Версии 0.9.1 и 0.9.0](#version-091-and-090)
* [Версия 0.8.16](#version-0816)
* [Версия 0.8.14](#version-0814)
* [Версия 0.8.13](#version-0813)
* [Версии 0.8.12, 0.8.11 и 0.8.10](#version-0812-and-0811-and-0810)
* [Версии 0.8.9 и 0.8.8](#version-089-and-088)
* [Версия 0.8.7](#version-087)
* [Версия 0.8.6](#version-086)
* [Версия 0.8.5](#version-085)
* [Версия 0.8.4](#version-084)
* [Версия 0.8.3](#version-083)
* [Версия 0.8.2](#version-082)
* [Версия 0.8.0](#version-080)
* [Версия 0.7.20160509.0](#version-07201605090)
* [Версия 0.7.20160325.0](#version-07201603250)
* [Версия 0.7.20160129.1](#version-07201601291)
* [Версия 0.7.20160105.0](#version-07201601050)
* [Версия 0.7.20151116.0](#version-07201511160)

## <a name="version-1101"></a>Version 1.10.1
9/19/2019

### <a name="hotfix"></a>Hotfix
* Some users encountered an error in 1.10.0 while attempting to view their data in their ADLS Gen 1 accounts. This error prevented the explorer panel from rendering properly. Эта ошибка исправлена. #1853 #1865

### <a name="new"></a>Новинка
* Storage Explorer now has a dedicated Settings UI. You can access it either from Edit → Settings, or by clicking on the Settings icon (the gear) in the left-hand vertical toolbar. This feature is the first step we're taking towards providing a variety of [user requested settings](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate). Starting in this release the following settings are supported:
  * Тема
  * Прокси-сервер
  * Logout on exit #6
  * Enable device code flow sign-in
  * Auto refresh #1526
  * Enable AzCopy
  * AzCopy SAS duration If there are other setttings you would like to see added, please [open an issue on GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=) describing the setting you want to see.
* Storage Explorer now supports Managed Disks. Вы сможете:
  * Upload an on-prem VHD to a new Disk
  * Download a Disk
  * Copy and paste disks across resource groups and regions
  * Delete Disks
  * Create a Snapshot of a Disk The uploading, downloading, and cross-region copying of disks are powered by AzCopy v10.
* Storage Explorer can now be installed via the Snap store on Linux. When you install via the Snap store, all dependencies are installed for you, including .NET Core! Currently we have verified that Storage Explorer runs well on Ubuntu and CentOS. If you encounter issues installing from the Snap store on other Linux distros, please [open an issue on GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). To learn more about installing from the Snap store, see our [getting started guide](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux). #68
* Two major changes have been made to attach with Azure Active Directory (Azure AD) which are intended to make the feature more useful for ADLS Gen2 users:
  * You now select the tenant that the resource you are attaching is in. This means that you no longer need to have RBAC access to the resource's subscription.
  * If you are attaching an ADLS Gen2 Blob Container, you can now attach to a specific path in the container.
* When managing ACLs for ADLS Gen2 files and folders, Storage Explorer will now show the friendly names for entities in the ACL. #957
* When adding via OID to an ADLS Gen2 ACL, Storage Explorer will now validate that the OID belongs to a valid entity in your tenant. #1603
* The keyboard shortcuts for navigating between tabs now use more standard key combinations. #1018
* Middle clicking on a tab will now close it. #1348
* If an AzCopy transfer contains skips and no failures, Storage Explorer will now show a warning icon to highlight that skips occured. #1490
* The integrated AzCopy has been updated to version 10.2.1. Additionally, you can now view the version of AzCopy installed in the About dialog. #1343

### <a name="fixes"></a>Исправления
* Many users have run into various "cannot read version of undefined" or "cannot read connection of undefined" errors when working with attached Storage Accounts. Although we are still continuing to investigate the root cause of this issue, in 1.10.0 we have improved the error handling around loading attached Storage Accounts. #1626, #985, and #1532
* It was possible for the explorer tree (left-hand side) to get into a state where focus would jump to the top node repeatedly. Эта ошибка исправлена. #1596
* When managing a blob's snapshots, screenreaders would not read the timestamp associated with the snapshot. Эта ошибка исправлена. #1202
* Proxy setting on macOS were not being set in time for the authentication process to use them. Эта ошибка исправлена. #1567
* If a Storage Account in a sovereign cloud was attached using name and key, AzCopy would not work. Эта ошибка исправлена. #1544
* When attaching via a connection string, Storage Explorer will now remove trailing spaces. #1387

### <a name="known-issues"></a>Известные проблемы
* The Auto Refresh setting does not yet affect all operations in the Blob Explorer.
* Managed Disk features are not supported in Azure Stack.
* If a Disk upload or paste fails and a new Disk was created prior to the failure, Storage Explorer will not delete the Disk for you.
* Depending on when you cancel a Disk upload or paste, it is possible to leave the new Disk in a corrupted state. If this happens, you either need to delete the new Disk, or manually call the Disk APIs to replace the contents of the Disk such that it is no longer corrupted.
* Depending on when you cancel a Disk upload or paste, it is possible to leave the new Disk in a corrupted state. If this happens, you either need to delete the new Disk, or manually call the Disk APIs to replace the contents of the Disk such that it is no longer corrupted.
* When performing a non-AzCopy Blob download, the MD5 for large files is not being verified. This is due to a bug in the Storage SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* When using RBAC, Storage Explorer requires some management layer permissions in order to access your storage resources. See the [troubleshooting guide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) for more info.
* Отключение от ресурса, подключенного через URI SAS, например контейнера больших двоичных объектов, может привести к ошибке, которая предотвращает правильное отображение других подключенных объектов. Чтобы обойти эту проблему, просто обновите узел группы. Для получения дополнительных сведений ознакомьтесь с исправлением № 537.
* Если вы используете VS для Mac и когда-либо создавали пользовательскую конфигурацию AAD, вы не сможете выполнить вход. Чтобы обойти эту проблему, удалите содержимое в папке ~/.IdentityService/AadConfigurations. Если это не разблокирует вашу учетную запись, оставьте комментарий об этой проблеме.
* В Azurite еще не полностью реализованы все API-интерфейсы хранилища. Из-за этого могут возникнуть непредвиденные ошибки или поведение при использовании Azurite в хранилище разработки.
* В редких случаях фокус дерева мог задержаться на функции быстрого доступа. Разблокировать фокус можно командой "Обновить все".
* Загрузка из папки OneDrive не работает из-за ошибки в NodeJS. Ошибка была исправлена, но еще не интегрирована в Electron. Чтобы обойти эту проблему при передаче и скачивании данных с использованием контейнера больших двоичных объектов, можно использовать экспериментальную функцию AzCopy.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный здесь обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* В Azure Stack не поддерживаются приведенные ниже возможности. Попытка использовать их при работе с ресурсами Azure Stack может привести к непредвиденным ошибкам.
   * Общие папки
   * Уровни доступа
   * обратимое удаление.
   * ADLS Gen2
   * Управляемые диски
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Running Storage Explorer on Linux requires certain dependencies to be installed first. Check the Storage Explorer [troubleshooting guide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) for more information.


## <a name="version-1100"></a>Version 1.10.0
9/12/2019

### <a name="new"></a>Новинка

* Storage Explorer now has a dedicated Settings UI. You can access it either from Edit → Settings, or by clicking on the Settings icon (the gear) in the left-hand vertical toolbar. This feature is the first step we're taking towards providing a variety of [user requested settings](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate). Starting in this release the following settings are supported:
    * Тема
    * Прокси-сервер
    * Logout on exit [#6](https://www.github.com/Microsoft/AzureStorageExplorer/issues/6)
    * Enable device code flow sign-in
    * Auto refresh [#1526](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1526)
    * Enable AzCopy
    * AzCopy SAS duration

    If there are other setttings you would like to see added, please [open an issue on GitHub describing the setting you want to see](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=).
* Storage Explorer now supports Managed Disks. Вы сможете:
    * Upload an on-prem VHD to a new Disk
    * Download a Disk
    * Copy and paste disks across resource groups and regions
    * Delete Disks
    * Create a Snapshot of a Disk

    The uploading, downloading, and cross-region copying of disks are powered by AzCopy v10.
* Storage Explorer can now be installed via the Snap store on Linux. When you install via the Snap store, all dependencies are installed for you, including .NET Core! Currently we have verified that Storage Explorer runs well on Ubuntu and CentOS. If you encounter issues installing from the Snap store on other Linux distros, please [open an issue on GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). To learn more about installing from the Snap store, see our [getting started guide](https://aka.ms/storageexplorer/snapinformation). [#68](https://www.github.com/Microsoft/AzureStorageExplorer/issues/68)
* Two major changes have been made to attach with Azure Active Directory (Azure AD) which are intended to make the feature more useful for ADLS Gen2 users: * You now select the tenant that the resource you are attaching is in. This means that you no longer need to have RBAC access to the resource's subscription.
        * If you are attaching an ADLS Gen2 Blob Container, you can now attach to a specific path in the container.
* When managing ACLs for ADLS Gen2 files and folders, Storage Explorer will now show the friendly names for entities in the ACL. [#957](https://www.github.com/Microsoft/AzureStorageExplorer/issues/957)
* When adding via OID to an ADLS Gen2 ACL, Storage Explorer will now validate that the OID belongs to a valid entity in your tenant. [#1603](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1603)
* The keyboard shortcuts for navigating between tabs now use more standard key combinations. [#1018](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1018)
* Middle clicking on a tab will now close it. [#1348](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1348)
* If an AzCopy transfer contains skips and no failures, Storage Explorer will now show a warning icon to highlight that skips occured. [#1490](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1490)
* The integrated AzCopy has been updated to version 10.2.1. Additionally, you can now view the version of AzCopy installed in the About dialog. [#1343](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1343)

### <a name="fixes"></a>Исправления

* Many users have run into various "cannot read version of undefined" or "cannot read connection of undefined" errors when working with attached Storage Accounts. Although we are still continuing to investigate the root cause of this issue, in 1.10.0 we have improved the error handling around loading attached Storage Accounts. [#1626](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1626), [#985](https://www.github.com/Microsoft/AzureStorageExplorer/issues/985), and [#1532](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1532)
* It was possible for the explorer tree (left-hand side) to get into a state where focus would jump to the top node repeatedly. Эта ошибка исправлена. [#1596](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1596)
* When managing a blob's snapshots, screenreaders would not read the timestamp associated with the snapshot. Эта ошибка исправлена. [#1202](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1202)
* Proxy setting on macOS were not being set in time for the authentication process to use them. Эта ошибка исправлена. [#1567](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1567)
* If a Storage Account in a sovereign cloud was attached using name and key, AzCopy would not work. Эта ошибка исправлена. [#1544](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1544)
* When attaching via a connection string, Storage Explorer will now remove trailing spaces. [#1387](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1387)

### <a name="known-issues"></a>Известные проблемы

* The Auto Refresh setting does not yet affect all operations in the Blob Explorer.
* Managed Disk features are not supported in Azure Stack.
* If a Disk upload or paste fails and a new Disk was created prior to the failure, Storage Explorer will not delete the Disk for you.
* Depending on when you cancel a Disk upload or paste, it is possible to leave the new Disk in a corrupted state. If this happens, you either need to delete the new Disk, or manually call the Disk APIs to replace the contents of the Disk such that it is no longer corrupted.
* When performing a non-AzCopy Blob download, the MD5 for large files is not being verified. This is due to a bug in the Storage SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* When using RBAC, Storage Explorer requires some management layer permissions in order to access your storage resources. See the [troubleshooting guide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) for more info.
* Отключение от ресурса, подключенного через URI SAS, например контейнера больших двоичных объектов, может привести к ошибке, которая предотвращает правильное отображение других подключенных объектов. Чтобы обойти эту проблему, просто обновите узел группы. Для получения дополнительных сведений ознакомьтесь с исправлением № 537.
* Если вы используете VS для Mac и когда-либо создавали пользовательскую конфигурацию AAD, вы не сможете выполнить вход. Чтобы обойти эту проблему, удалите содержимое в папке ~/.IdentityService/AadConfigurations. Если это не разблокирует вашу учетную запись, оставьте комментарий об этой проблеме.
* В Azurite еще не полностью реализованы все API-интерфейсы хранилища. Из-за этого могут возникнуть непредвиденные ошибки или поведение при использовании Azurite в хранилище разработки.
* В редких случаях фокус дерева мог задержаться на функции быстрого доступа. Разблокировать фокус можно командой "Обновить все".
* Загрузка из папки OneDrive не работает из-за ошибки в NodeJS. Ошибка была исправлена, но еще не интегрирована в Electron. Чтобы обойти эту проблему при передаче и скачивании данных с использованием контейнера больших двоичных объектов, можно использовать экспериментальную функцию AzCopy.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный здесь обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* В Azure Stack не поддерживаются приведенные ниже возможности. Попытка использовать их при работе с ресурсами Azure Stack может привести к непредвиденным ошибкам.
   * Общие папки
   * Уровни доступа
   * обратимое удаление.
   * ADLS Gen2
   * Управляемые диски
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Running Storage Explorer on Linux requires certain dependencies to be installed first. Check the Storage Explorer [troubleshooting guide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) for more information.

## <a name="version-190"></a>Version 1.9.0
7/1/2019

### <a name="download-azure-storage-explorer-190"></a>Download Azure Storage Explorer 1.9.0
- [Azure Storage Explorer 1.9.0 for Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage Explorer 1.9.0 for Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 1.9.0 for Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Новинка

* You can now attach Blob containers via Azure AD (RBAC or ACL permissions). This feature is intended to help users who have access to containers but not the Storage Accounts that the containers are in. See our Getting Started Guide for more information on this feature.
* Acquire and break lease now work with RBAC. [#1354](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1354)
* Managing access policies and setting public access level now work with RBAC. [#1355](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1355)
* Deleting blob folders now work with RBAC. [#1450](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1450)
* Changing blob access tier now work with RBAC. [#1446](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1446)
* You can now quickly reset Quick Access via "Help" → "Reset". [#1327](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1327)

### <a name="preview-features"></a>Предварительные версии компонентов

* Device code flow sign in is now available to preview. To enable it, go to "Preview" → "Use Device Code Flow Sign-in". We encourage any users who have had issues with blank sign-in windows to try this feature, as it may prove to be a more reliable form of sign-in.
* Storage Explorer integrated with AzCopy is currently available to preview. To enable it, go to "Preview" → "Use AzCopy for Improved Blob Upload and Download". Blob transfers completed with AzCopy should be faster and more performant.

### <a name="fixes"></a>Исправления

* Fixed being unable to load more than 50 subscriptions for one account. [#1416](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1416)
* Fixed the "Sign in" button not working on the infobar that appears when a direct link fails. [#1358](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1358)
* Fixed not being to upload .app files on macOS. [#1119](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1119)
* Fixed "Retry All" not working for a failed blob rename. [#992](https://www.github.com/Microsoft/AzureStorageExplorer/issues/992)
* Fixed "Cancel" not working while opening a blob. [#1464](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1464)
* Fixed multiple spelling and tooltip issues throughout the product. Many thanks to all who reported these issues! [#1303](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1303), [#1328](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1328), [#1329](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1329), [#1331](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1331), [#1336](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1336), [#1352](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1352), [#1368](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1368), [#1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1395)

### <a name="known-issues"></a>Известные проблемы

* When performing a non-AzCopy Blob download, the MD5 for large files is not being verified. This is due to a bug in the Storage SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* When using RBAC, Storage Explorer requires some management layer permissions in order to access your storage resources. See the [troubleshooting guide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) for more info.
* Attempting to access ADLS Gen2 Blobs when behind a proxy may fail.
* Отключение от ресурса, подключенного через URI SAS, например контейнера больших двоичных объектов, может привести к ошибке, которая предотвращает правильное отображение других подключенных объектов. Чтобы обойти эту проблему, просто обновите узел группы. Для получения дополнительных сведений ознакомьтесь с исправлением № 537.
* Если вы используете VS для Mac и когда-либо создавали пользовательскую конфигурацию AAD, вы не сможете выполнить вход. Чтобы обойти эту проблему, удалите содержимое в папке ~/.IdentityService/AadConfigurations. Если это не разблокирует вашу учетную запись, оставьте комментарий об этой проблеме.
* В Azurite еще не полностью реализованы все API-интерфейсы хранилища. Из-за этого могут возникнуть непредвиденные ошибки или поведение при использовании Azurite в хранилище разработки.
* В редких случаях фокус дерева мог задержаться на функции быстрого доступа. Разблокировать фокус можно командой "Обновить все".
* Загрузка из папки OneDrive не работает из-за ошибки в NodeJS. Ошибка была исправлена, но еще не интегрирована в Electron. Чтобы обойти эту проблему при передаче и скачивании данных с использованием контейнера больших двоичных объектов, можно использовать экспериментальную функцию AzCopy.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный здесь обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* В Azure Stack не поддерживаются приведенные ниже возможности. Попытка использовать их при работе с ресурсами Azure Stack может привести к непредвиденным ошибкам.
   * Общие папки
   * Уровни доступа
   * обратимое удаление.
   * ADLS Gen2
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Running Storage Explorer on Linux requires certain dependencies to be installed first. Check the Storage Explorer [troubleshooting guide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) for more information.

## <a name="version-181"></a>Version 1.8.1
5/13/2019

### <a name="hotfixes"></a>Исправления
* In some cases, clicking "Load more" at the resource level would not return the next page of resources. Эта ошибка исправлена. [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* On Windows, AzCopy downloads would fail if a single file or folder was being downloaded and the name of the file or folder had a character which was invalid for a Windows path. Эта ошибка исправлена. [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* In extremely rare cases, while performing a rename of a File Share or a rename in a File Share, if the copies for the rename failed, or if Storage Explore was unable to confirm the success of the copies with Azure, there was the potential for Storage Explorer to delete the original files before the copy had finished. Эта ошибка исправлена.

### <a name="new"></a>Новинка

* The integrated AzCopy version has been updated to version 10.1.0.
* Ctrl/Cmd+R can now be used to refresh the currently focused editor. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* The Azure Stack Storage API version has been changed to 2017-04-17.
* The Manage Access Dialog for ADLS Gen2 will now keep the Mask in sync in a way similar to other POSIX permissions tools. The UI will also warn you if a change is made that causes the permissions of a user or group to exceed the bounds of the Mask. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* For AzCopy uploads, the flag to calculate and set the MD5 hash is now enabled. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Предварительные версии компонентов

* Device code flow sign in is now available to preview. To enable it, go to "Preview" → "Use Device Code Flow Sign-in". We encourage any users who have had issues with blank sign-in windows to try this feature, as it may prove to be a more reliable form of sign-in.
* Storage Explorer integrated with AzCopy is currently available to preview. To enable it, go to "Preview" → "Use AzCopy for Improved Blob Upload and Download". Blob transfers completed with AzCopy should be faster and more performant.

### <a name="fixes"></a>Исправления

* The Access Policies dialog will no longer set an expiry date on Storage Access Policies that do not have an expiry. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Some changes have been made to the Generate SAS dialog to make sure Stored Access Policies are used correctly when generating a SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* When attempting to upload a non-512 byte aligned file to a page Blob, Storage Explorer will now expose a more relevant error. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Copying a Blob container which utilized a display name would fail. Now, the actual name of the Blob container is used. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Attempting to perform certain actions on an ADLS Gen2 folder which had unicode characters in its name would fail. All actions should now work. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Известные проблемы

* When performing a non-AzCopy Blob download, the MD5 for large files is not being verified. This is due to a bug in the Storage SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* When using RBAC, Storage Explorer requires some management layer permissions in order to access your storage resources. See the [troubleshooting guide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) for more info.
* Attempting to access ADLS Gen2 Blobs when behind a proxy may fail.
* Отключение от ресурса, подключенного через URI SAS, например контейнера больших двоичных объектов, может привести к ошибке, которая предотвращает правильное отображение других подключенных объектов. Чтобы обойти эту проблему, просто обновите узел группы. Для получения дополнительных сведений ознакомьтесь с исправлением № 537.
* Если вы используете VS для Mac и когда-либо создавали пользовательскую конфигурацию AAD, вы не сможете выполнить вход. Чтобы обойти эту проблему, удалите содержимое в папке ~/.IdentityService/AadConfigurations. Если это не разблокирует вашу учетную запись, оставьте комментарий об этой проблеме.
* В Azurite еще не полностью реализованы все API-интерфейсы хранилища. Из-за этого могут возникнуть непредвиденные ошибки или поведение при использовании Azurite в хранилище разработки.
* В редких случаях фокус дерева мог задержаться на функции быстрого доступа. Разблокировать фокус можно командой "Обновить все".
* Загрузка из папки OneDrive не работает из-за ошибки в NodeJS. Ошибка была исправлена, но еще не интегрирована в Electron. Чтобы обойти эту проблему при передаче и скачивании данных с использованием контейнера больших двоичных объектов, можно использовать экспериментальную функцию AzCopy.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный здесь обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* В Azure Stack не поддерживаются приведенные ниже возможности. Попытка использовать их при работе с ресурсами Azure Stack может привести к непредвиденным ошибкам.
   * Общие папки
   * Уровни доступа
   * обратимое удаление.
   * ADLS Gen2
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Running Storage Explorer on Linux requires certain dependencies to be installed first. Check the Storage Explorer [troubleshooting guide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) for more information.

## <a name="version-180"></a>Version 1.8.0
5/1/2019

### <a name="new"></a>Новинка

* The integrated AzCopy version has been updated to version 10.1.0.
* Ctrl/Cmd+R can now be used to refresh the currently focused editor. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* The Azure Stack Storage API version has been changed to 2017-04-17.
* The Manage Access Dialog for ADLS Gen2 will now keep the Mask in sync in a way similar to other POSIX permissions tools. The UI will also warn you if a change is made that causes the permissions of a user or group to exceed the bounds of the Mask. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* For AzCopy uploads, the flag to calculate and set the MD5 hash is now enabled. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Предварительные версии компонентов

* Device code flow sign in is now available to preview. To enable it, go to "Preview" → "Use Device Code Flow Sign-in". We encourage any users who have had issues with blank sign-in windows to try this feature, as it may prove to be a more reliable form of sign-in.
* Storage Explorer integrated with AzCopy is currently available to preview. To enable it, go to "Preview" → "Use AzCopy for Improved Blob Upload and Download". Blob transfers completed with AzCopy should be faster and more performant.

### <a name="fixes"></a>Исправления

* The Access Policies dialog will no longer set an expiry date on Storage Access Policies that do not have an expiry. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Some changes have been made to the Generate SAS dialog to make sure Stored Access Policies are used correctly when generating a SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* When attempting to upload a non-512 byte aligned file to a page Blob, Storage Explorer will now expose a more relevant error. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Copying a Blob container which utilized a display name would fail. Now, the actual name of the Blob container is used. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Attempting to perform certain actions on an ADLS Gen2 folder which had unicode characters in its name would fail. All actions should now work. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Известные проблемы

* When performing a non-AzCopy Blob download, the MD5 for large files is not being verified. This is due to a bug in the Storage SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* When using RBAC, Storage Explorer requires some management layer permissions in order to access your storage resources. See the [troubleshooting guide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) for more info.
* Attempting to access ADLS Gen2 Blobs when behind a proxy may fail.
* Отключение от ресурса, подключенного через URI SAS, например контейнера больших двоичных объектов, может привести к ошибке, которая предотвращает правильное отображение других подключенных объектов. Чтобы обойти эту проблему, просто обновите узел группы. Для получения дополнительных сведений ознакомьтесь с исправлением № 537.
* Если вы используете VS для Mac и когда-либо создавали пользовательскую конфигурацию AAD, вы не сможете выполнить вход. Чтобы обойти эту проблему, удалите содержимое в папке ~/.IdentityService/AadConfigurations. Если это не разблокирует вашу учетную запись, оставьте комментарий об этой проблеме.
* В Azurite еще не полностью реализованы все API-интерфейсы хранилища. Из-за этого могут возникнуть непредвиденные ошибки или поведение при использовании Azurite в хранилище разработки.
* В редких случаях фокус дерева мог задержаться на функции быстрого доступа. Разблокировать фокус можно командой "Обновить все".
* Загрузка из папки OneDrive не работает из-за ошибки в NodeJS. Ошибка была исправлена, но еще не интегрирована в Electron. Чтобы обойти эту проблему при передаче и скачивании данных с использованием контейнера больших двоичных объектов, можно использовать экспериментальную функцию AzCopy.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный здесь обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* В Azure Stack не поддерживаются приведенные ниже возможности. Попытка использовать их при работе с ресурсами Azure Stack может привести к непредвиденным ошибкам.
   * Общие папки
   * Уровни доступа
   * обратимое удаление.
   * ADLS Gen2
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Running Storage Explorer on Linux requires certain dependencies to be installed first. Check the Storage Explorer [troubleshooting guide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) for more information.

## <a name="version-170"></a>Версия 1.7.0
3/5/2019

### <a name="download-azure-storage-explorer-170"></a>Download Azure Storage Explorer 1.7.0
- [Azure Storage Explorer 1.7.0 for Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage Explorer 1.7.0 for Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 1.7.0 for Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Новинка

* You can now change the owner and owning group when managing access for an ADLS Gen2 container, file, or folder.
* On Windows, updating Storage Explorer from within the product is now an incremental install. This should result in a faster update experience. If you prefer a clean install, then you can download the [installer](https://azure.microsoft.com/features/storage-explorer/) yourself and then install manually. #1089

### <a name="preview-features"></a>Предварительные версии компонентов

* Device code flow sign in is now available to preview. To enable it, go to "Preview" → "Use Device Code Flow Sign-in". We encourage any users who have had issues with blank sign-in windows to try this feature, as it may prove to be a more reliable form of sign-in. #938
* Storage Explorer integrated with AzCopy is currently available to preview. To enable it, go to "Preview" → "Use AzCopy for Improved Blob Upload and Download". Blob transfers completed with AzCopy should be faster and more performant.

### <a name="fixes"></a>Исправления

* You can now choose the blob type you want to upload as when AzCopy is enabled. #1111
* Previously, if you had enabled static websites for an ADLS Gen2 Storage account and then attached it with name and key, Storage Explorer would not have detected that hierarchical namespace was enabled. Эта ошибка исправлена. #1081
* In the blob editor, sorting by either retention days remaining or status was broken. Эта ошибка исправлена. #1106
* After 1.5.0, Storage Explorer no longer waited for server side copies to finish before reporting success during a rename or copy & paste. Эта ошибка исправлена. #976
* When using the experimental AzCopy feature, the command copied after clicking "Copy command to clipboard" was not always runnable on its own. Now, all commands needed to run the transfer manually will be copied. #1079
* Previously, ADLS Gen2 blobs were not accessible if you were behind a proxy. This was due to a bug in a new networking library used by the Storage SDK. In 1.7.0, an attempt to mitigate this issue has been made, but some people may continue to see issues. A full fix will be released in a future update. #1090
* In 1.7.0, the save file dialog now correctly remembers the last location you saved a file to. #16
* In the properties panel, the SKU tier of a Storage account was being shown as the account's kind. Эта ошибка исправлена. #654
* Sometimes, it was impossible to break the lease of a blob, even if you entered the name of the blob correctly. Эта ошибка исправлена. #1070

### <a name="known-issues"></a>Известные проблемы

* When using RBAC, Storage Explorer requires some management layer permissions in order to access your storage resources. See the [troubleshooting guide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) for more info.
* Attempting to access ADLS Gen2 Blobs when behind a proxy may fail.
* Отключение от ресурса, подключенного через URI SAS, например контейнера больших двоичных объектов, может привести к ошибке, которая предотвращает правильное отображение других подключенных объектов. Чтобы обойти эту проблему, просто обновите узел группы. Для получения дополнительных сведений ознакомьтесь с исправлением № 537.
* Отключение от ресурса, подключенного через URI SAS, например контейнера больших двоичных объектов, может привести к ошибке, которая предотвращает правильное отображение других подключенных объектов. Чтобы обойти эту проблему, просто обновите узел группы. Дополнительные сведения см. в описании ошибки #537.
* Если вы используете VS для Mac и когда-либо создавали пользовательскую конфигурацию AAD, вы не сможете выполнить вход. Чтобы обойти эту проблему, удалите содержимое в папке ~/.IdentityService/AadConfigurations. Если это не разблокирует вашу учетную запись, оставьте комментарий об этой проблеме.
* В Azurite еще не полностью реализованы все API-интерфейсы хранилища. Из-за этого могут возникнуть непредвиденные ошибки или поведение при использовании Azurite в хранилище разработки.
* В редких случаях фокус дерева мог задержаться на функции быстрого доступа. Разблокировать фокус можно командой "Обновить все".
* Загрузка из папки OneDrive не работает из-за ошибки в NodeJS. Ошибка была исправлена, но еще не интегрирована в Electron. Чтобы обойти эту проблему при передаче и скачивании данных с использованием контейнера больших двоичных объектов, можно использовать экспериментальную функцию AzCopy.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный здесь обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* В Azure Stack не поддерживаются приведенные ниже возможности. Попытка использовать их при работе с ресурсами Azure Stack может привести к непредвиденным ошибкам.
   * Общие папки
   * Уровни доступа
   * обратимое удаление.
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Для пользователей Linux необходимо установить [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Пользователям Ubuntu 14.04 понадобится обновить GCC. Для этого нужно выполнить команды ниже, а затем перезагрузить компьютер:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Пользователям Ubuntu 17.04 понадобится установить GConf. Для этого нужно выполнить следующие команды, а затем перезагрузить компьютер:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>Версия 1.6.2
9\.01.2019

### <a name="hotfixes"></a>Исправления
* В версии 1.6.1 сущности, добавленные к спискам управления доступом ADLS 2-го поколения по ObjectId, которые не были пользователями, всегда добавлялись как группы. Теперь только группы добавляются как группы, а сущности, такие как корпоративные приложения и субъекты-службы, добавляются как пользователи. [№ 1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* Если в учетной записи хранения ADLS 2-го поколения отсутствовали контейнеры и она была подключена с именем и ключом, Обозреватель службы хранилища не определял, что это учетная запись хранения ADLS 2-го поколения. Эта ошибка исправлена. [№ 1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* В версии 1.6.0 при конфликтах во время копирования и вставки не выводится запрос на разрешение. Вместо этого конфликтное копирование просто завершается сбоем. Теперь при первом конфликте будет выведен запрос на его разрешение. [№ 1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* Из-за ограничений API все проверки идентификаторов ObjectId в диалоговом окне "Управление доступом" были отключены. Теперь проверке будут подвергаться только имена участников-пользователей. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* В диалоговом окне "Управление доступом" в ADLS 2-го поколения нельзя было изменить разрешения для группы. Эта ошибка исправлена. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* В редакторе ADLS 2-го поколения добавлена поддержка загрузки путем перетаскивания. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* В поле URL-адреса для файлов и папок ADLS 2-го поколения в диалоговом окне свойств иногда отсутствовал символ "/". Эта ошибка исправлена. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Если получение текущих разрешений для контейнера, файла или папки ADLS 2-го поколения завершается ошибкой, эта ошибка теперь отображается в журнале действий правильно. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Временный путь, создаваемый для открытия файлов, был сокращен, чтобы уменьшить вероятность создания пути, длина которого превышает значение параметра MAX_PATH в Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Теперь при отсутствии пользователей, выполнивших вход в систему, и выделенных ресурсов отображается необходимое диалоговое окно "Подключение". [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* В версии 1.6.0 сохранение свойств для BLOB-объектов и файлов, которые не являются HNS, запускало кодировку значения каждого свойства. В результате кодировались ненужные значения, содержавшие только символы ASCII. Теперь значения будут кодироваться только в том случае, если содержат символы, отличные от ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Отправка папки в контейнер для BLOB-объектов, который не является HNS, завершалась ошибкой, если использовался SAS и этот SAS не имел разрешений на чтение. Эта ошибка исправлена. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Отмена операции передачи AzCopy не работала. Эта ошибка исправлена. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* Операция AzCopy при попытке загрузить папку из контейнера BLOB-объектов ADLS 2-го поколения завершалась ошибкой, если в имени этой папки были пробелы. Эта ошибка исправлена. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* Редактор CosmosDB в версии 1.6.0 не работал. Теперь эта ошибка исправлена. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Новинка

* Теперь обозреватель службы хранилища можно использовать для доступа к данным BLOB-объектов с помощью [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Если вы вошли в систему, а обозреватель службы хранилища не может получить ключи для вашей учетной записи хранения, для проверки подлинности при взаимодействии с вашими данными будет использоваться токен oAuth.
* Обозреватель хранилищ теперь поддерживает учетные записи хранения ADLS 2-го поколения. Если обозреватель службы хранилища обнаружит, что иерархическое пространство имен для учетной записи хранения включено, рядом с именем вашей учетной записи хранения появится значок "(Предварительная версия ADLS 2-го поколения)". Обозреватель службы хранилища может определить, включено ли иерархическое пространство имен, если вы выполнили вход в систему или присвоили своей учетной записи хранения имя и ключ. Для учетных записей хранения ADLS 2-го поколения можно использовать обозреватель службы хранилища в следующих целях.
  * Создание и удаление контейнеров
  * Управление свойствами и разрешениями контейнера (слева)
  * Просмотр и перемещение между данными внутри контейнеров
  * Создание папок
  * Отправка, загрузка, переименование и удаление файлов и папок
  * Управление свойствами и разрешениями файлов и папок (справа)
    
    Другие типичные функции BLOB-объектов, такие как обратимое удаление и моментальные снимки, в настоящее время недоступны. Управление разрешениями также доступно только после входа в систему. Кроме того, при работе в учетной записи хранения ADLS 2-го поколения обозреватель службы хранилища будет применять AzCopy для любой отправки и загрузки и по умолчанию использовать имя и ключ, если таковые имеются, для всех операций.
* В результате активной реакции пользователей была возвращена возможность прерывать аренду сразу для нескольких BLOB-объектов.

### <a name="known-issues"></a>Известные проблемы

* При загрузке из учетной записи хранения ADLS 2-го поколения операция AzCopy могла завершится сбоем, если один из передаваемых файлов уже существовал. Эта ошибка будет устранена в следующем исправлении.
* Отключение от ресурса, подключенного через URI SAS, например контейнера больших двоичных объектов, может привести к ошибке, которая предотвращает правильное отображение других подключенных объектов. Чтобы обойти эту проблему, просто обновите узел группы. Дополнительные сведения см. в описании ошибки #537.
* Если вы используете VS для Mac и когда-либо создавали пользовательскую конфигурацию AAD, вы не сможете выполнить вход. Чтобы обойти эту проблему, удалите содержимое в папке ~/.IdentityService/AadConfigurations. Если это не разблокирует вашу учетную запись, оставьте комментарий об этой проблеме.
* В Azurite еще не полностью реализованы все API-интерфейсы хранилища. Из-за этого могут возникнуть непредвиденные ошибки или поведение при использовании Azurite в хранилище разработки.
* В редких случаях фокус дерева мог задержаться на функции быстрого доступа. Разблокировать фокус можно командой "Обновить все".
* Загрузка из папки OneDrive не работает из-за ошибки в NodeJS. Ошибка была исправлена, но еще не интегрирована в Electron. Чтобы обойти эту проблему при передаче и скачивании данных с использованием контейнера больших двоичных объектов, можно использовать экспериментальную функцию AzCopy.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный здесь обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* В Azure Stack не поддерживаются приведенные ниже возможности. Попытка использовать их при работе с ресурсами Azure Stack может привести к непредвиденным ошибкам.
   * Общие папки
   * Уровни доступа
   * обратимое удаление.
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Для пользователей Linux необходимо установить [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Пользователям Ubuntu 14.04 понадобится обновить GCC. Для этого нужно выполнить команды ниже, а затем перезагрузить компьютер:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Пользователям Ubuntu 17.04 понадобится установить GConf. Для этого нужно выполнить следующие команды, а затем перезагрузить компьютер:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>Версия 1.6.1
18.12.2018

### <a name="hotfixes"></a>Исправления
* Из-за ограничений API все проверки идентификаторов ObjectId в диалоговом окне "Управление доступом" были отключены. Теперь проверке будут подвергаться только имена участников-пользователей. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* В диалоговом окне "Управление доступом" в ADLS 2-го поколения нельзя было изменить разрешения для группы. Эта ошибка исправлена. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* В редакторе ADLS 2-го поколения добавлена поддержка загрузки путем перетаскивания. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* В поле URL-адреса для файлов и папок ADLS 2-го поколения в диалоговом окне свойств иногда отсутствовал символ "/". Эта ошибка исправлена. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Если получение текущих разрешений для контейнера, файла или папки ADLS 2-го поколения завершается ошибкой, эта ошибка теперь отображается в журнале действий правильно. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Временный путь, создаваемый для открытия файлов, был сокращен, чтобы уменьшить вероятность создания пути, длина которого превышает значение параметра MAX_PATH в Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Теперь при отсутствии пользователей, выполнивших вход в систему, и выделенных ресурсов отображается необходимое диалоговое окно "Подключение". [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* В версии 1.6.0 сохранение свойств для BLOB-объектов и файлов, которые не являются HNS, запускало кодировку значения каждого свойства. В результате кодировались ненужные значения, содержавшие только символы ASCII. Теперь значения будут кодироваться только в том случае, если содержат символы, отличные от ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Отправка папки в контейнер для BLOB-объектов, который не является HNS, завершалась ошибкой, если использовался SAS и этот SAS не имел разрешений на чтение. Эта ошибка исправлена. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Отмена операции передачи AzCopy не работала. Эта ошибка исправлена. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* Операция AzCopy при попытке загрузить папку из контейнера BLOB-объектов ADLS 2-го поколения завершалась ошибкой, если в имени этой папки были пробелы. Эта ошибка исправлена. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* Редактор CosmosDB в версии 1.6.0 не работал. Теперь эта ошибка исправлена. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Новинка

* Теперь обозреватель службы хранилища можно использовать для доступа к данным BLOB-объектов с помощью [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Если вы вошли в систему, а обозреватель службы хранилища не может получить ключи для вашей учетной записи хранения, для проверки подлинности при взаимодействии с вашими данными будет использоваться токен oAuth.
* Обозреватель хранилищ теперь поддерживает учетные записи хранения ADLS 2-го поколения. Если обозреватель службы хранилища обнаружит, что иерархическое пространство имен для учетной записи хранения включено, рядом с именем вашей учетной записи хранения появится значок "(Предварительная версия ADLS 2-го поколения)". Обозреватель службы хранилища может определить, включено ли иерархическое пространство имен, если вы выполнили вход в систему или присвоили своей учетной записи хранения имя и ключ. Для учетных записей хранения ADLS 2-го поколения можно использовать обозреватель службы хранилища в следующих целях.
  * Создание и удаление контейнеров
  * Управление свойствами и разрешениями контейнера (слева)
  * Просмотр и перемещение между данными внутри контейнеров
  * Создание папок
  * Отправка, загрузка, переименование и удаление файлов и папок
  * Управление свойствами и разрешениями файлов и папок (справа)
    
    Другие типичные функции BLOB-объектов, такие как обратимое удаление и моментальные снимки, в настоящее время недоступны. Управление разрешениями также доступно только после входа в систему. Кроме того, при работе в учетной записи хранения ADLS 2-го поколения обозреватель службы хранилища будет применять AzCopy для любой отправки и загрузки и по умолчанию использовать имя и ключ, если таковые имеются, для всех операций.
* В результате активной реакции пользователей была возвращена возможность прерывать аренду сразу для нескольких BLOB-объектов.

### <a name="known-issues"></a>Известные проблемы

* При загрузке из учетной записи хранения ADLS 2-го поколения операция AzCopy могла завершится сбоем, если один из передаваемых файлов уже существовал. Эта ошибка будет устранена в следующем исправлении.
* Отключение от ресурса, подключенного через URI SAS, например контейнера больших двоичных объектов, может привести к ошибке, которая предотвращает правильное отображение других подключенных объектов. Чтобы обойти эту проблему, просто обновите узел группы. Дополнительные сведения см. в описании ошибки #537.
* Если вы используете VS для Mac и когда-либо создавали пользовательскую конфигурацию AAD, вы не сможете выполнить вход. Чтобы обойти эту проблему, удалите содержимое в папке ~/.IdentityService/AadConfigurations. Если это не разблокирует вашу учетную запись, оставьте комментарий об этой проблеме.
* В Azurite еще не полностью реализованы все API-интерфейсы хранилища. Из-за этого могут возникнуть непредвиденные ошибки или поведение при использовании Azurite в хранилище разработки.
* В редких случаях фокус дерева мог задержаться на функции быстрого доступа. Разблокировать фокус можно командой "Обновить все".
* Загрузка из папки OneDrive не работает из-за ошибки в NodeJS. Ошибка была исправлена, но еще не интегрирована в Electron. Чтобы обойти эту проблему при передаче и скачивании данных с использованием контейнера больших двоичных объектов, можно использовать экспериментальную функцию AzCopy.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный здесь обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* В Azure Stack не поддерживаются приведенные ниже возможности. Попытка использовать их при работе с ресурсами Azure Stack может привести к непредвиденным ошибкам.
   * Общие папки
   * Уровни доступа
   * обратимое удаление.
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Для пользователей Linux необходимо установить [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Пользователям Ubuntu 14.04 понадобится обновить GCC. Для этого нужно выполнить команды ниже, а затем перезагрузить компьютер:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Пользователям Ubuntu 17.04 понадобится установить GConf. Для этого нужно выполнить следующие команды, а затем перезагрузить компьютер:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>Версия 1.6.0
05.12.2018

### <a name="new"></a>Новинка

* Теперь обозреватель службы хранилища можно использовать для доступа к данным BLOB-объектов с помощью [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Если вы вошли в систему, а обозреватель службы хранилища не может получить ключи для вашей учетной записи хранения, для проверки подлинности при взаимодействии с вашими данными будет использоваться токен oAuth.
* Обозреватель хранилищ теперь поддерживает учетные записи хранения ADLS 2-го поколения. Если обозреватель службы хранилища обнаружит, что иерархическое пространство имен для учетной записи хранения включено, рядом с именем вашей учетной записи хранения появится значок "(Предварительная версия ADLS 2-го поколения)". Обозреватель службы хранилища может определить, включено ли иерархическое пространство имен, если вы выполнили вход в систему или присвоили своей учетной записи хранения имя и ключ. Для учетных записей хранения ADLS 2-го поколения можно использовать обозреватель службы хранилища в следующих целях.
  * Создание и удаление контейнеров
  * Управление свойствами и разрешениями контейнера (слева)
  * Просмотр и перемещение между данными внутри контейнеров
  * Создание папок
  * Отправка, загрузка, переименование и удаление файлов и папок
  * Управление свойствами и разрешениями файлов и папок (справа)
    
    Другие типичные функции BLOB-объектов, такие как обратимое удаление и моментальные снимки, в настоящее время недоступны. Управление разрешениями также доступно только после входа в систему. Кроме того, при работе в учетной записи хранения ADLS 2-го поколения обозреватель службы хранилища будет применять AzCopy для любой отправки и загрузки и по умолчанию использовать имя и ключ, если таковые имеются, для всех операций.
* В результате активной реакции пользователей была возвращена возможность прерывать аренду сразу для нескольких BLOB-объектов.

### <a name="known-issues"></a>Известные проблемы

* При загрузке из учетной записи хранения ADLS 2-го поколения операция AzCopy могла завершится сбоем, если один из передаваемых файлов уже существовал. Эта ошибка будет устранена в следующем исправлении.
* Отключение от ресурса, подключенного через URI SAS, например контейнера больших двоичных объектов, может привести к ошибке, которая предотвращает правильное отображение других подключенных объектов. Чтобы обойти эту проблему, просто обновите узел группы. Дополнительные сведения см. в описании ошибки #537.
* Если вы используете VS для Mac и когда-либо создавали пользовательскую конфигурацию AAD, вы не сможете выполнить вход. Чтобы обойти эту проблему, удалите содержимое в папке ~/.IdentityService/AadConfigurations. Если это не разблокирует вашу учетную запись, оставьте комментарий об этой проблеме.
* В Azurite еще не полностью реализованы все API-интерфейсы хранилища. Из-за этого могут возникнуть непредвиденные ошибки или поведение при использовании Azurite в хранилище разработки.
* В редких случаях фокус дерева мог задержаться на функции быстрого доступа. Разблокировать фокус можно командой "Обновить все".
* Загрузка из папки OneDrive не работает из-за ошибки в NodeJS. Ошибка была исправлена, но еще не интегрирована в Electron. Чтобы обойти эту проблему при передаче и скачивании данных с использованием контейнера больших двоичных объектов, можно использовать экспериментальную функцию AzCopy.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный здесь обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* В Azure Stack не поддерживаются приведенные ниже возможности. Попытка использовать их при работе с ресурсами Azure Stack может привести к непредвиденным ошибкам.
   * Общие папки
   * Уровни доступа
   * обратимое удаление.
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Для пользователей Linux необходимо установить [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Пользователям Ubuntu 14.04 понадобится обновить GCC. Для этого нужно выполнить команды ниже, а затем перезагрузить компьютер:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Пользователям Ubuntu 17.04 понадобится установить GConf. Для этого нужно выполнить следующие команды, а затем перезагрузить компьютер:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>Версия 1.5.0
29.10.2018

### <a name="new"></a>Новинка

* Теперь вы можете использовать [AzCopy версии 10 (предварительная версия)](https://github.com/Azure/azure-storage-azcopy) для передачи и скачивания больших двоичных объектов. Для включения этой функции перейдите к меню "Экспериментальный" и щелкните "Use AzCopy for Improved Blob Upload and Download" (Использовать AzCopy для усовершенствованной передачи и скачивания больших двоичных объектов). Если этот параметр включен, AzCopy будет использоваться в следующих сценариях.
   * Передача папок и файлов в контейнеры больших двоичных объектов с помощью панели инструментов или перетаскивания.
   * Скачивание папок и файлов с помощью панели инструментов или контекстного меню.

* Кроме того, при использовании AzCopy:
   * Можно скопировать в буфер обмена команду AzCopy, используемую для передачи. Просто щелкните "Copy AzCopy Command to Clipboard" (Копировать команду AzCopy в буфер обмена) в журнале действий.
   * Необходимо будет вручную обновить редактор больших двоичных объектов после передачи.
   * Передача файлов в добавочные BLOB-объекты не поддерживается. VHD-файлы будут переданы в виде страничных BLOB-объектов, а все остальные файлы будут переданы в виде блочных BLOB-объектов.
   * Ошибки и конфликты, возникающие во время передачи или скачивания, не будут отображаться вплоть до завершения передачи или скачивания.

Наконец, поддержка использования AzCopy с файловыми ресурсами будет реализована в будущем.
* Обозреватель хранилищ теперь использует Electron версии 2.0.11.
* Прерывание аренды теперь возможно только по одному большому двоичному объекту за раз. Кроме того, необходимо ввести имя большого двоичного объекта, аренда которого прерывается. Это изменение было внесено, чтобы уменьшить вероятность случайного прерывания аренды, особенно для виртуальных машин. № 394
* Теперь в случае сбоев при входе в систему можно попробовать сбросить параметры аутентификации. Перейдите в меню "Справка" и щелкните "Сброс", чтобы получить доступ к этой возможности. № 419

### <a name="fix"></a>Исправление

* После многочисленных отзывов пользователей был повторно включен узел эмулятора по умолчанию. Вы по-прежнему можете добавить дополнительные подключения к эмулятору в диалоговом окне "Подключение", но если симулятор настроен для использования портов по умолчанию, можно также использовать узел "Emulator * Default Ports" (Эмулятор и порты по умолчанию) в разделе "Local & Attached/Storage Accounts" (Локальные и подключенные учетные записи хранения). № 669
* Обозреватель службы хранилища больше не позволит вам задать значения метаданных больших двоичных объектов, которые содержат начальные или конечные пробелы. № 760
* Кнопка "Вход" всегда была включена на одинаковых страницах диалогового окна "Подключение". Теперь она отключена, когда это необходимо. № 761
* Быстрый доступ больше не приведет к ошибке в консоли, если элементы быстрого доступа не добавлены.

### <a name="known-issues"></a>Известные проблемы

* Отключение от ресурса, подключенного через URI SAS, например контейнера больших двоичных объектов, может привести к ошибке, которая предотвращает правильное отображение других подключенных объектов. Чтобы обойти эту проблему, просто обновите узел группы. Дополнительные сведения см. в описании ошибки #537.
* Если вы используете VS для Mac и когда-либо создавали пользовательскую конфигурацию AAD, вы не сможете выполнить вход. Чтобы обойти эту проблему, удалите содержимое в папке ~/.IdentityService/AadConfigurations. Если это не разблокирует вашу учетную запись, оставьте комментарий об этой проблеме.
* В Azurite еще не полностью реализованы все API-интерфейсы хранилища. Из-за этого могут возникнуть непредвиденные ошибки или поведение при использовании Azurite в хранилище разработки.
* В редких случаях фокус дерева мог задержаться на функции быстрого доступа. Разблокировать фокус можно командой "Обновить все".
* Загрузка из папки OneDrive не работает из-за ошибки в NodeJS. Ошибка была исправлена, но еще не интегрирована в Electron. Чтобы обойти эту проблему при передаче и скачивании данных с использованием контейнера больших двоичных объектов, можно использовать экспериментальную функцию AzCopy.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный здесь обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* В Azure Stack не поддерживаются приведенные ниже возможности. Попытка использовать их при работе с ресурсами Azure Stack может привести к непредвиденным ошибкам.
   * Общие папки
   * Уровни доступа
   * обратимое удаление.
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Для пользователей Linux необходимо установить [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Пользователям Ubuntu 14.04 понадобится обновить GCC. Для этого нужно выполнить команды ниже, а затем перезагрузить компьютер:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Пользователям Ubuntu 17.04 понадобится установить GConf. Для этого нужно выполнить следующие команды, а затем перезагрузить компьютер:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>Версия 1.4.4
10/15/2018

### <a name="hotfixes"></a>Исправления
* Выполнен откат версии API управления ресурсами Azure для разблокирования пользователей Azure для государственных организаций США. [696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* В индикаторах загрузки теперь используется анимация CSS, чтобы сократить объем GPU, используемый Обозревателем службы хранилища. [653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Новинка
* Внешние подключенные ресурсы, например для подключений SAS и эмуляторов, были значительно улучшены. Теперь вы можете:
   * Настройте отображаемое имя подключаемого ресурса. [№ 31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Подключите несколько локальных эмуляторов, используя разные порты. [№ 193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Добавьте подключенные ресурсы на панель быстрого доступа. [№ 392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Обозреватель службы хранилища теперь поддерживает обратимое удаление. Вы сможете:
   * Настройте политику обратимого удаления, щелкнув правой кнопкой мыши узел контейнеров больших двоичных объектов для вашей учетной записи хранения.
   * Просмотрите обратимо удаленные большие двоичные объекты в редакторе больших двоичных объектов, выбрав Active and deleted blobs (Активные и удаленные большие двоичные объекты) в раскрывающемся списке рядом с панелью навигации.
   * Отмените удаление обратимо удаленных больших двоичных объектов.

### <a name="fixes"></a>Исправления
* Действие Configure CORS Settings (Настроить параметры CORS) теперь недоступно в учетных записях хранения уровня "Премиум", так как эти учетные записи хранения не поддерживают CORS. [№ 142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Для подключенных служб SAS теперь есть свойство подписанного URL-адреса. [№ 184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Действие Set Default Access Tier (Задать уровень доступа по умолчанию) теперь доступно для учетных записей больших двоичных объектов и учетных записей хранения GPV2, которые были закреплены для быстрого доступа. [№ 229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* В некоторых случаях Обозреватель службы хранилища не может отобразить классические учетные записи хранения. [№ 323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Известные проблемы
* При использовании эмуляторов, например эмулятора службы хранилища Azure или Azurite, необходимо настроить их на ожидание подключения в портах по умолчанию. В противном случае обозреватель службы хранилища не сможет к ним подключиться.
* Если вы используете VS для Mac и когда-либо создавали пользовательскую конфигурацию AAD, вы не сможете выполнить вход. Чтобы обойти эту проблему, удалите содержимое в папке ~/.IdentityService/AadConfigurations. Если это не разблокирует вашу учетную запись, оставьте комментарий об этой [проблеме](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* В Azurite еще не полностью реализованы все API-интерфейсы хранилища. Из-за этого могут возникнуть непредвиденные ошибки или поведение при использовании Azurite в хранилище разработки.
* В редких случаях фокус дерева мог задержаться на функции быстрого доступа. Разблокировать фокус можно командой "Обновить все".
* Загрузка из папки OneDrive не работает из-за ошибки в NodeJS. Ошибка была исправлена, но еще не интегрирована в Electron.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный [здесь](https://github.com/Azure/azure-storage-node/issues/317) обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* Несмотря на то, что сейчас Azure Stack не поддерживает общие файловые ресурсы, соответствующий узел все равно отображается под подключенной учетной записью хранения Azure Stack.
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Для пользователей Linux необходимо установить [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Пользователям Ubuntu 14.04 понадобится обновить GCC. Для этого нужно выполнить команды ниже, а затем перезагрузить компьютер:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Пользователям Ubuntu 17.04 понадобится установить GConf. Для этого нужно выполнить следующие команды, а затем перезагрузить компьютер:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>Версия 1.4.3
10/11/2018

### <a name="hotfixes"></a>Исправления
* Выполнен откат версии API управления ресурсами Azure для разблокирования пользователей Azure для государственных организаций США. [696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* В индикаторах загрузки теперь используется анимация CSS, чтобы сократить объем GPU, используемый Обозревателем службы хранилища. [653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Новинка
* Внешние подключенные ресурсы, например для подключений SAS и эмуляторов, были значительно улучшены. Теперь вы можете:
   * Настройте отображаемое имя подключаемого ресурса. [№ 31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Подключите несколько локальных эмуляторов, используя разные порты. [№ 193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Добавьте подключенные ресурсы на панель быстрого доступа. [№ 392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Обозреватель службы хранилища теперь поддерживает обратимое удаление. Вы сможете:
   * Настройте политику обратимого удаления, щелкнув правой кнопкой мыши узел контейнеров больших двоичных объектов для вашей учетной записи хранения.
   * Просмотрите обратимо удаленные большие двоичные объекты в редакторе больших двоичных объектов, выбрав Active and deleted blobs (Активные и удаленные большие двоичные объекты) в раскрывающемся списке рядом с панелью навигации.
   * Отмените удаление обратимо удаленных больших двоичных объектов.

### <a name="fixes"></a>Исправления
* Действие Configure CORS Settings (Настроить параметры CORS) теперь недоступно в учетных записях хранения уровня "Премиум", так как эти учетные записи хранения не поддерживают CORS. [№ 142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Для подключенных служб SAS теперь есть свойство подписанного URL-адреса. [№ 184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Действие Set Default Access Tier (Задать уровень доступа по умолчанию) теперь доступно для учетных записей больших двоичных объектов и учетных записей хранения GPV2, которые были закреплены для быстрого доступа. [№ 229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* В некоторых случаях Обозреватель службы хранилища не может отобразить классические учетные записи хранения. [№ 323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Известные проблемы
* При использовании эмуляторов, например эмулятора службы хранилища Azure или Azurite, необходимо настроить их на ожидание подключения в портах по умолчанию. В противном случае обозреватель службы хранилища не сможет к ним подключиться.
* Если вы используете VS для Mac и когда-либо создавали пользовательскую конфигурацию AAD, вы не сможете выполнить вход. Чтобы обойти эту проблему, удалите содержимое в папке ~/.IdentityService/AadConfigurations. Если это не разблокирует вашу учетную запись, оставьте комментарий об этой [проблеме](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* В Azurite еще не полностью реализованы все API-интерфейсы хранилища. Из-за этого могут возникнуть непредвиденные ошибки или поведение при использовании Azurite в хранилище разработки.
* В редких случаях фокус дерева мог задержаться на функции быстрого доступа. Разблокировать фокус можно командой "Обновить все".
* Загрузка из папки OneDrive не работает из-за ошибки в NodeJS. Ошибка была исправлена, но еще не интегрирована в Electron.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный [здесь](https://github.com/Azure/azure-storage-node/issues/317) обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* Несмотря на то, что сейчас Azure Stack не поддерживает общие файловые ресурсы, соответствующий узел все равно отображается под подключенной учетной записью хранения Azure Stack.
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Для пользователей Linux необходимо установить [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Пользователям Ubuntu 14.04 понадобится обновить GCC. Для этого нужно выполнить команды ниже, а затем перезагрузить компьютер:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Пользователям Ubuntu 17.04 понадобится установить GConf. Для этого нужно выполнить следующие команды, а затем перезагрузить компьютер:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>Версия 1.4.2
09/24/2018

### <a name="hotfixes"></a>Исправления
* Обновите API управления ресурсами Azure до версии 2018-07-01, чтобы добавить поддержку новых типов учетной записи хранения Azure. [652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>Новинка
* Внешние подключенные ресурсы, например для подключений SAS и эмуляторов, были значительно улучшены. Теперь вы можете:
   * Настройте отображаемое имя подключаемого ресурса. [№ 31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Подключите несколько локальных эмуляторов, используя разные порты. [№ 193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Добавьте подключенные ресурсы на панель быстрого доступа. [№ 392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Обозреватель службы хранилища теперь поддерживает обратимое удаление. Вы сможете:
   * Настройте политику обратимого удаления, щелкнув правой кнопкой мыши узел контейнеров больших двоичных объектов для вашей учетной записи хранения.
   * Просмотрите обратимо удаленные большие двоичные объекты в редакторе больших двоичных объектов, выбрав Active and deleted blobs (Активные и удаленные большие двоичные объекты) в раскрывающемся списке рядом с панелью навигации.
   * Отмените удаление обратимо удаленных больших двоичных объектов.

### <a name="fixes"></a>Исправления
* Действие Configure CORS Settings (Настроить параметры CORS) теперь недоступно в учетных записях хранения уровня "Премиум", так как эти учетные записи хранения не поддерживают CORS. [№ 142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Для подключенных служб SAS теперь есть свойство подписанного URL-адреса. [№ 184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Действие Set Default Access Tier (Задать уровень доступа по умолчанию) теперь доступно для учетных записей больших двоичных объектов и учетных записей хранения GPV2, которые были закреплены для быстрого доступа. [№ 229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* В некоторых случаях Обозреватель службы хранилища не может отобразить классические учетные записи хранения. [№ 323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Известные проблемы
* При использовании эмуляторов, например эмулятора службы хранилища Azure или Azurite, необходимо настроить их на ожидание подключения в портах по умолчанию. В противном случае обозреватель службы хранилища не сможет к ним подключиться.
* Если вы используете VS для Mac и когда-либо создавали пользовательскую конфигурацию AAD, вы не сможете выполнить вход. Чтобы обойти эту проблему, удалите содержимое в папке ~/.IdentityService/AadConfigurations. Если это не разблокирует вашу учетную запись, оставьте комментарий об этой [проблеме](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* В Azurite еще не полностью реализованы все API-интерфейсы хранилища. Из-за этого могут возникнуть непредвиденные ошибки или поведение при использовании Azurite в хранилище разработки.
* В редких случаях фокус дерева мог задержаться на функции быстрого доступа. Разблокировать фокус можно командой "Обновить все".
* Загрузка из папки OneDrive не работает из-за ошибки в NodeJS. Ошибка была исправлена, но еще не интегрирована в Electron.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный [здесь](https://github.com/Azure/azure-storage-node/issues/317) обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* Несмотря на то, что сейчас Azure Stack не поддерживает общие файловые ресурсы, соответствующий узел все равно отображается под подключенной учетной записью хранения Azure Stack.
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Для пользователей Linux необходимо установить [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Пользователям Ubuntu 14.04 понадобится обновить GCC. Для этого нужно выполнить команды ниже, а затем перезагрузить компьютер:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Пользователям Ubuntu 17.04 понадобится установить GConf. Для этого нужно выполнить следующие команды, а затем перезагрузить компьютер:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>Версия 1.4.1
28.08.2018

### <a name="hotfixes"></a>Исправления
* При первом запуске Обозревателю службы хранилища не удалось создать ключ, используемый для шифрования конфиденциальных данных. Это приведет к проблемам при использовании быстрого доступа и подключении ресурсов. [№ 535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* Если вашей учетной записи для домашнего клиента не требуется Многофакторная идентификация, однако она требуется для некоторых других клиентов, Обозреватель службы хранилища не сможет перечислить подписки. Теперь после входа с такой учетной записью Обозреватель службы хранилища запросит повторное введение учетных данных и выполнение MFA. [№ 74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* Обозревателю службы хранилища не удалось подключить ресурсы из Azure для Германии и Azure для государственных организаций США. [№ 572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* Если вы вошли в две учетные записи, которые имеют один адрес электронной почты, у Обозревателя службы хранилища иногда будут возникать проблемы с отображением ресурсов в представлении в виде дерева. [№ 580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* На медленных компьютерах Windows появление экрана-заставки иногда будет занимать значительно больше времени. [№ 586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* Диалоговое окно подключения появится, даже если есть подключенные учетные записи или службы. [№ 588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>Новинка
* Внешние подключенные ресурсы, например для подключений SAS и эмуляторов, были значительно улучшены. Теперь вы можете:
   * Настройте отображаемое имя подключаемого ресурса. [№ 31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Подключите несколько локальных эмуляторов, используя разные порты. [№ 193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Добавьте подключенные ресурсы на панель быстрого доступа. [№ 392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Обозреватель службы хранилища теперь поддерживает обратимое удаление. Вы сможете:
   * Настройте политику обратимого удаления, щелкнув правой кнопкой мыши узел контейнеров больших двоичных объектов для вашей учетной записи хранения.
   * Просмотрите обратимо удаленные большие двоичные объекты в редакторе больших двоичных объектов, выбрав Active and deleted blobs (Активные и удаленные большие двоичные объекты) в раскрывающемся списке рядом с панелью навигации.
   * Отмените удаление обратимо удаленных больших двоичных объектов.

### <a name="fixes"></a>Исправления
* Действие Configure CORS Settings (Настроить параметры CORS) теперь недоступно в учетных записях хранения уровня "Премиум", так как эти учетные записи хранения не поддерживают CORS. [№ 142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Для подключенных служб SAS теперь есть свойство подписанного URL-адреса. [№ 184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Действие Set Default Access Tier (Задать уровень доступа по умолчанию) теперь доступно для учетных записей больших двоичных объектов и учетных записей хранения GPV2, которые были закреплены для быстрого доступа. [№ 229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* В некоторых случаях Обозреватель службы хранилища не может отобразить классические учетные записи хранения. [№ 323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Известные проблемы
* При использовании эмуляторов, например эмулятора службы хранилища Azure или Azurite, необходимо настроить их на ожидание подключения в портах по умолчанию. В противном случае обозреватель службы хранилища не сможет к ним подключиться.
* Если вы используете VS для Mac и когда-либо создавали пользовательскую конфигурацию AAD, вы не сможете выполнить вход. Чтобы обойти эту проблему, удалите содержимое в папке ~/.IdentityService/AadConfigurations. Если это не разблокирует вашу учетную запись, оставьте комментарий об этой [проблеме](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* В Azurite еще не полностью реализованы все API-интерфейсы хранилища. Из-за этого могут возникнуть непредвиденные ошибки или поведение при использовании Azurite в хранилище разработки.
* В редких случаях фокус дерева мог задержаться на функции быстрого доступа. Разблокировать фокус можно командой "Обновить все".
* Загрузка из папки OneDrive не работает из-за ошибки в NodeJS. Ошибка была исправлена, но еще не интегрирована в Electron.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный [здесь](https://github.com/Azure/azure-storage-node/issues/317) обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* Несмотря на то, что сейчас Azure Stack не поддерживает общие файловые ресурсы, соответствующий узел все равно отображается под подключенной учетной записью хранения Azure Stack.
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Для пользователей Linux необходимо установить [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Пользователям Ubuntu 14.04 понадобится обновить GCC. Для этого нужно выполнить команды ниже, а затем перезагрузить компьютер:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Пользователям Ubuntu 17.04 понадобится установить GConf. Для этого нужно выполнить следующие команды, а затем перезагрузить компьютер:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>Версия 1.3.0
09.07.2018.

### <a name="new"></a>Новинка
* Теперь поддерживается доступ к веб-контейнерам, используемым статическими веб-сайтами. Это позволяет легко передавать файлы и папки, используемые вашим веб-сайтом, и управлять ими. [223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* Панель приложений на macOS была реорганизована. Изменения включают в себя меню "Файл", некоторые сочетания клавиш и несколько новых команд в меню приложения. [99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* Конечная точка центра для входа в Azure для государственных организаций США была изменена на https://login.microsoftonline.us/.
* Специальные возможности. Теперь, когда средство чтения с экрана активно, навигация с помощью клавиатуры работает с таблицами, используемыми для отображения элементов с правой стороны. Вы можете использовать клавиши со стрелками для навигации по строкам и столбцам, клавишу "ВВОД", чтобы запустить действия по умолчанию, клавишу контекстного меню, чтобы открыть контекстное меню для элемента, клавиши и Shift или Ctrl для множественного выбора. [103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Исправления
*  На некоторых компьютерах дочерние процессы занимали много времени. При этом отображалась ошибка: child process failed to start in a timely manner (дочернему процессу не удалось запуститься своевременно). Время, затрачиваемое на запуск дочернего процесса, теперь увеличено с 20 до 90 секунд. Если вы все еще сталкиваетесь с этой проблемой, оставьте комментарий о проблеме в GitHub. [281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* При использовании SAS, у которого не было прав на чтение, невозможно было передать большой двоичный объект. Логика передачи была изменена для работы в этом сценарии. [305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* Настройка общедоступного уровня доступа для контейнера приведет к удалению всех политик доступа и наоборот. Теперь общедоступный уровень доступа и политики доступа сохраняются при настройке любого из двух. [197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* В диалоговом окне "Свойства" было усечено AccessTierChangeTime. Эта ошибка исправлена. [145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* В диалоговом окне "Создать новый каталог" отсутствовал префикс "Обозреватель службы хранилища Azure". Эта ошибка исправлена. [299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Специальные возможности. При использовании VoiceOver было трудно ориентироваться в диалоговом окне "Добавление сущности". Были внесены улучшения. [206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Специальные возможности. Цвет фона кнопки свертывания и развертывания на панели "Действия" и "Свойства" был несовместим с аналогичными элементами управления пользовательского интерфейса в контрастной черной теме. Цвет изменен. [123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Специальные возможности. В контрастной черной теме не был виден стиль фокусировки кнопки X в диалоговом окне "Свойства". Эта ошибка исправлена. [243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Специальные возможности. На вкладках "Действия" и "Свойства" отсутствовали несколько значений Aria, в результате чего нужно было использовать средство для чтения подраздела с экрана. Отсутствующие значения были добавлены. [316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Специальные возможности. В свернутых узлах дерева с левой стороны для атрибута aria-expanded не было доступно значение false. Эта ошибка исправлена. [352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Известные проблемы
* Отключение от ресурса, подключенного через URI SAS, например контейнера больших двоичных объектов, может привести к ошибке, которая предотвращает правильное отображение других подключенных объектов. Чтобы обойти эту проблему, просто обновите узел группы. Дополнительные сведения об этой проблеме см. в [этой статье](https://github.com/Microsoft/AzureStorageExplorer/issues/537).
* Если вы используете VS для Mac и когда-либо создавали пользовательскую конфигурацию AAD, вы не сможете выполнить вход. Чтобы обойти эту проблему, удалите содержимое в папке ~/.IdentityService/AadConfigurations. Если это не разблокирует вашу учетную запись, оставьте комментарий об этой [проблеме](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* В Azurite еще не полностью реализованы все API-интерфейсы хранилища. Из-за этого могут возникнуть непредвиденные ошибки или поведение при использовании Azurite в хранилище разработки.
* В редких случаях фокус дерева мог задержаться на функции быстрого доступа. Разблокировать фокус можно командой "Обновить все".
* Загрузка из папки OneDrive не работает из-за ошибки в NodeJS. Ошибка была исправлена, но еще не интегрирована в Electron.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный [здесь](https://github.com/Azure/azure-storage-node/issues/317) обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* Azure Stack не поддерживает следующие функции (при попытке использовать их в работе с Azure Stack могут возникнуть неожиданные ошибки):
   * Общие папки
   * Уровни доступа
   * обратимое удаление.
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Для пользователей Linux необходимо установить [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Пользователям Ubuntu 14.04 понадобится обновить GCC. Для этого нужно выполнить команды ниже, а затем перезагрузить компьютер:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Пользователям Ubuntu 17.04 понадобится установить GConf. Для этого нужно выполнить следующие команды, а затем перезагрузить компьютер:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>Версии 1.2.0
12/06/2018

### <a name="new"></a>Новинка
* Если обозревателю службы хранилища не удается загрузить подписки только из подмножества ваших клиентов, будут отображаться любые успешно загруженные подписки, а также сообщение об ошибке для клиентов, у которых произошел сбой. [159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* Когда в Windows доступно обновление, вы можете выбрать "Обновить при закрытии". Если вы выбрали этот вариант, средство установки обновления запустится, когда вы закроете обозреватель службы хранилища. [21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* В контекстное меню редактора общей папки при просмотре моментального снимка общей папки добавлен пункт "Восстановить моментальный снимок".[131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* Кнопка "Очистить очередь" теперь всегда активна.[135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* Поддержка входа в ADFS Azure Stack снова включена. Требуется Azure Stack версии 1804 или более поздней. [150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Исправления
* Если вы просматривали снимки для общей папки, имя которой было префиксом другой общей папки в этой же учетной записи хранения, моментальные снимки второй общей папки также будут отображаться. Теперь эта проблема устранена. [255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* При присоединении через SAS восстановление файла из моментального снимка общей папки приведет к ошибке. Теперь эта проблема устранена. [211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* При просмотре моментальных снимков для большого двоичного объекта было включено действие повышения уровня моментального снимка, когда были выбраны базовый большой двоичный объект и один моментальный снимок. Теперь это действие включено, только если выбран один моментальный снимок. [230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Если вы запускали одно задание (например, загрузку большого двоичного объекта) и оно завершалось сбоем, повторная попытка не предпринималась, пока вы не запускали другое задание того же типа. Теперь все задания автоматически выполняются повторно, независимо от их количества в очереди.
* В редакторах, открытых для только что созданных контейнеров больших двоичных объектов в GPV2, и учетных записях хранилища BLOB-объектов, не было столбца "Уровень доступа". Теперь эта проблема устранена. [109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* Столбец "Уровень доступа" иногда не отображался, если учетная запись хранения или контейнер BLOB-объектов были привязаны через SAS. Теперь столбец всегда отображается, но содержит пустое значение, если уровень доступа не задан. [160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* Настройка уровня доступа в только что загруженных блочных BLOB-объектах была отключена. Теперь эта проблема устранена. [171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* При вызове кнопки "Оставить вкладку открытой" с клавиатуры терялся фокус клавиатуры. Теперь фокус перемещается на вкладку, которую оставили открытой. [163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* Для запроса в конструкторе запросов функция VoiceOver не давала понятное описание текущего оператора. Теперь она более информативна. [207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* Ссылки на разбиение на страницы в различных редакторах были неинформативными. Теперь они содержат больше полезной информации. [205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* В диалоговом окне "Добавить сущность" функция VoiceOver не объявляла, в какой столбец входил элемент ввода. Теперь имя текущего столбца входит в описание элемента. [206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* У переключателей и полей для галочки пропадали видимые границы при фокусировке. Теперь эта проблема устранена. [237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Известные проблемы
* При использовании эмуляторов, например эмулятора службы хранилища Azure или Azurite, необходимо настроить их на ожидание подключения в портах по умолчанию. В противном случае обозреватель службы хранилища не сможет к ним подключиться.
* Если вы используете VS для Mac и когда-либо создавали пользовательскую конфигурацию AAD, вы не сможете выполнить вход. Чтобы обойти эту проблему, удалите содержимое в папке ~/.IdentityService/AadConfigurations. Если это не разблокирует вашу учетную запись, оставьте комментарий об этой [проблеме](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* В Azurite еще не полностью реализованы все API-интерфейсы хранилища. Из-за этого могут возникнуть непредвиденные ошибки или поведение при использовании Azurite в хранилище разработки.
* В редких случаях фокус дерева мог задержаться на функции быстрого доступа. Разблокировать фокус можно командой "Обновить все".
* Загрузка из папки OneDrive не работает из-за ошибки в NodeJS. Ошибка была исправлена, но еще не интегрирована в Electron.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный [здесь](https://github.com/Azure/azure-storage-node/issues/317) обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* Несмотря на то, что сейчас Azure Stack не поддерживает общие файловые ресурсы, соответствующий узел все равно отображается под подключенной учетной записью хранения Azure Stack.
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Для пользователей Linux необходимо установить [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Пользователям Ubuntu 14.04 понадобится обновить GCC. Для этого нужно выполнить команды ниже, а затем перезагрузить компьютер:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Пользователям Ubuntu 17.04 понадобится установить GConf. Для этого нужно выполнить следующие команды, а затем перезагрузить компьютер:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>Версия 1.1.0
09.05.2018

### <a name="new"></a>Новинка
* Обозреватель службы хранилища теперь поддерживает использование Azurite. Примечание. Подключение к Azurite жестко привязано к конечным точкам разработки по умолчанию.
* В Обозревателе службы хранилища теперь поддерживаются уровни доступа учетных записей хранения только для больших двоичных объектов и GPV2. Дополнительные сведения об уровнях доступа см. [здесь](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).
* Время начала действия больше не требуется при создании SAS.

### <a name="fixes"></a>Исправления
* Получение подписок для учетных записей государственных организаций США было нарушено. Теперь эта проблема устранена. [№ 61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* Время истечения срока для политик доступа неправильно сохранялось. Теперь эта проблема устранена. [№ 50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* При создании URL-адреса SAS для элемента в контейнере имя элемента не добавлялось к URL-адресу. Теперь эта проблема устранена. [№ 44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* При создании SAS время истечения срока действия, которое было в прошлом, иногда было значением по умолчанию. Это было связно с тем, что Обозреватель службы хранилища применял последнее используемое время начала и окончания срока действия в качестве значений по умолчанию. Теперь каждый раз, когда вы открываете диалоговое окно SAS, создается новый набор значений по умолчанию. [№ 35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* При копировании между учетными записями хранения создается SAS на 24 часа. Если копирование длилось более 24 часов, оно завершалось сбоем. Мы увеличили срок действия SAS до последней недели, чтобы уменьшить вероятность сбоя копирования из-за истечения срока действия SAS. [№ 62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* Для некоторых действий нажатие кнопки "Отмена" срабатывало не всегда. Теперь эта проблема устранена. [№ 125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* Для некоторых действий скорость передачи была неправильной. Теперь эта проблема устранена. [№ 124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* Написание "Предыдущий" в меню "Представление" было неправильным. Теперь написание правильное. [№ 71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* На последней странице установщика Windows была кнопка "Далее". Она была изменена на кнопку "Готово". [№ 70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* Фокус вкладки не отображался для кнопок в диалоговых окнах при использовании темы HC Black. Теперь он отображается. [№ 64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* Регистр элемента "Автоматическое разрешение" для действий в журнале действий был неправильным. Теперь он правильный. [№ 51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* При удалении объекта из таблицы в диалоговом окне с запросом на подтверждение отображался значок ошибки. Теперь в нем отображается значок предупреждения. [№ 148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Известные проблемы
* Если вы используете VS для Mac и когда-либо создавали пользовательскую конфигурацию AAD, вы не сможете выполнить вход. Чтобы обойти эту проблему, удалите содержимое в папке ~/.IdentityService/AadConfigurations. Если это не разблокирует вашу учетную запись, оставьте комментарий об этой [проблеме](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* В Azurite еще не полностью реализованы все API-интерфейсы хранилища. Из-за этого могут возникнуть непредвиденные ошибки или поведение при использовании Azurite в хранилище разработки.
* В редких случаях фокус дерева мог задержаться на функции быстрого доступа. Разблокировать фокус можно командой "Обновить все".
* Загрузка из папки OneDrive не работает из-за ошибки в NodeJS. Ошибка была исправлена, но еще не интегрирована в Electron.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный [здесь](https://github.com/Azure/azure-storage-node/issues/317) обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* Несмотря на то, что сейчас Azure Stack не поддерживает общие файловые ресурсы, соответствующий узел все равно отображается под подключенной учетной записью хранения Azure Stack.
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Для пользователей Linux необходимо установить [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Пользователям Ubuntu 14.04 понадобится обновить GCC. Для этого нужно выполнить команды ниже, а затем перезагрузить компьютер:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Пользователям Ubuntu 17.04 понадобится установить GConf. Для этого нужно выполнить следующие команды, а затем перезагрузить компьютер:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>Версия 1.0.0
16.04.2018

### <a name="new"></a>Новинка
* Улучшенная проверка подлинности, которая позволяет Обозревателю службы хранилища использовать то же хранилище учетных записей, что и в Visual Studio 2017. Чтобы использовать эту функцию, необходимо повторно войти в свои учетные записи и заново установить отфильтрованные подписки.
* Для учетных записей Azure Stack, поддерживаемых AAD, Обозреватель службы хранилища теперь будет получать подписки Azure Stack, если включен целевой объект Azure Stack. Больше не требуется создавать настраиваемую среду для входа.
* Добавлено несколько сочетаний клавиш для ускорения навигации. К ним относятся переключение различных панелей и перемещение между редакторами. Дополнительные сведения см. в меню "Вид".
* Отзывы об Обозревателе службы хранилища теперь доступны на GitHub. Чтобы открыть нашу страницу вопросов, нажмите кнопку для отправки отзыва в нижнем левом углу или перейдите по адресу [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues). Вы можете оставить свои предложения и отзывы, сообщать о проблемах и задавать вопросы в любых других формах.
* Если возникла проблема с SSL-сертификатом и вам не удалось найти сертификат, вызвавший ошибку, теперь можно запустить Обозреватель службы хранилища из командной строки, используя флаг `--ignore-certificate-errors`. При запуске с этим флагом Обозреватель службы хранилища будет игнорировать ошибки SSL-сертификата.
* Теперь в контекстном меню есть пункт "Скачать" для таких элементов, как большие двоичные объекты и файлы.
* Улучшена доступность и поддержка программ чтения с экрана. Если вам необходимы специальные возможности, см. нашу [документацию по специальным возможностям](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-accessibility) для получения дополнительных сведений.
* Обозреватель службы хранилища теперь использует Electron 1.8.3

### <a name="breaking-changes"></a>Критические изменения
* Обозреватель службы хранилища перешел на новую библиотеку проверки подлинности. В рамках перехода на новую библиотеку вам необходимо будет повторно войти в свои учетные записи и заново установить отфильтрованные подписки.
* Изменен метод шифрования конфиденциальных данных. Возможно, вам потребуется повторно добавить некоторые элементы быстрого доступа и (или) повторно подключить некоторые ресурсы.

### <a name="fixes"></a>Исправления
* У некоторых пользователей за прокси-серверами прерывалась групповая отправка или скачивание больших двоичных объектов и появлялось сообщение об ошибке "Unable to resolve" (Не удается разрешить). Теперь эта проблема устранена.
* Если при использовании прямой ссылки требовалось войти в систему, после нажатия кнопки входа в запросе открывалось пустое диалоговое окно. Теперь эта проблема устранена.
* Теперь, если Обозреватель службы хранилища не удается запустить в Linux из-за сбоя процесса GPU, вы получите уведомление о сбое с указанием использовать параметр командной строки --disable-gpu. После этого Обозреватель службы хранилища автоматически перезапустится с включенным параметром.
* Недопустимые политики доступа было трудно обнаружить в диалоговом окне "Политики доступа". Теперь идентификаторы недопустимых политик доступа выделяются красным цветом для более удобного восприятия.
* В журнале действий иногда были большие пустые области между разными частями действия. Теперь эта проблема устранена.
* В редакторе запросов таблиц, если предложение метки времени было оставлено в недопустимом состоянии и затем предпринималась попытка изменить другое предложение, происходило зависание редактора. Теперь редактор восстанавливает последнее допустимое состояние для предложение метки времени, когда обнаруживает изменение в другом предложении.
* Если вы останавливались при вводе поискового запроса в представлении в виде дерева, начинался поиск и фокус перемещался из текстового поля. Теперь, чтобы явно начать поиск, необходимо нажать клавишу "ВВОД" или кнопку начала поиска.
* Иногда команда Get Shared Access Signature (Получение подписанного URL-адреса) отключалась, если щелкнуть правой кнопкой мыши файл в общей папке. Теперь эта проблема устранена.
* Если узел дерева ресурсов с фокусом отфильтровывался во время поиска, становилось невозможно перейти клавишей TAB в дерево ресурсов и использовать клавиши со стрелками для навигации по нему. Теперь если узел дерева ресурсов с фокусом скрывается, фокус автоматически переходит на первый узел дерева ресурсов.
* Иногда на панели инструментов редактора отображался дополнительный разделитель. Теперь эта проблема устранена.
* Текстовое поле навигатора иногда переполнялось. Теперь эта проблема устранена.
* Редакторы больших двоичных объектов и общих папок могли постоянно обновляться при одновременной отправке большого числа файлов. Теперь эта проблема устранена.
* Компонент статистики по папкам не имел назначения в представлении File Share Snapshots Management (Управление моментальными снимками общей папки). Теперь он отключен.
* В Linux меню "Файл" не отображалось. Теперь эта проблема устранена.
* При отправке папки в общую папку по умолчанию передавалось только содержимое этой папки. Теперь по умолчанию содержимое папки отправляется в соответствующую папку в общей папке.
* В некоторых диалоговых окнах кнопки размещались в обратном порядке. Теперь эта проблема устранена.
* Различные исправления, связанные с безопасностью.

### <a name="known-issues"></a>Известные проблемы
* В редких случаях фокус дерева мог задержаться на функции быстрого доступа. Разблокировать фокус можно командой "Обновить все".
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный здесь обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* Несмотря на то, что сейчас Azure Stack не поддерживает общие файловые ресурсы, соответствующий узел все равно отображается под подключенной учетной записью хранения Azure Stack.
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Для пользователей Linux необходимо установить [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Пользователям Ubuntu 14.04 понадобится обновить GCC. Для этого нужно выполнить команды ниже, а затем перезагрузить компьютер:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Пользователям Ubuntu 17.04 понадобится установить GConf. Для этого нужно выполнить следующие команды, а затем перезагрузить компьютер:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>Версия 0.9.6
28.02.2018

### <a name="fixes"></a>Исправления
* Возникала проблема, из-за которой не удавалось перечислить в редакторе ожидаемые большие двоичные объекты или файлы. Теперь эта проблема устранена.
* Возникала проблема, из-за которой при переключении между представлениями моментальных снимков неправильно отображались элементы. Теперь эта проблема устранена.

### <a name="known-issues"></a>Известные проблемы
* Обозреватель хранилищ не поддерживает учетные записи AD FS.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный [здесь](https://github.com/Azure/azure-storage-node/issues/317) обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* Панель "Параметры учетной записи" может подать запрос на повторный ввод учетных данных для фильтрации подписок.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* Несмотря на то, что сейчас Azure Stack не поддерживает общие файловые ресурсы, соответствующий узел все равно отображается под подключенной учетной записью хранения Azure Stack.
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Пользователям Ubuntu 14.04 понадобится обновить GCC. Для этого нужно выполнить команды ниже, а затем перезагрузить компьютер:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Пользователям Ubuntu 17.04 понадобится установить GConf. Для этого нужно выполнить следующие команды, а затем перезагрузить компьютер:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>Версия 0.9.5
06.02.2018

### <a name="new"></a>Новинка

* Поддержка моментальных снимков файловых ресурсов:
    * создание моментальных снимков файловых ресурсов и управление ими;
    * удобное переключение между представлениями моментальных снимков файловых ресурсов при просмотре;
    * восстановление предыдущих версий файлов.
* Поддержка предварительной версии для Azure Data Lake Store:
    * подключение к ресурсам ADLS в нескольких учетных записях;
    * подключение к ресурсам ADLS и их совместное использование с помощью универсальных кодов ресурсов (URI) ADL.
    * рекурсивное выполнение основных операций с файлами и папками;
    * закрепление отдельных папок для быстрого доступа;
    * отображение статистики по папкам.

### <a name="fixes"></a>Исправления
* Ускорение запуска.
* Исправление различных ошибок.

### <a name="known-issues"></a>Известные проблемы
* Обозреватель хранилищ не поддерживает учетные записи AD FS.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный здесь обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* Панель "Параметры учетной записи" может подать запрос на повторный ввод учетных данных для фильтрации подписок.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* Несмотря на то, что сейчас Azure Stack не поддерживает общие файловые ресурсы, соответствующий узел все равно отображается под подключенной учетной записью хранения Azure Stack.
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Пользователям Ubuntu 14.04 понадобится обновить GCC. Для этого нужно выполнить команды ниже, а затем перезагрузить компьютер:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Пользователям Ubuntu 17.04 понадобится установить GConf. Для этого нужно выполнить следующие команды, а затем перезагрузить компьютер:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>Версии 0.9.4 и 0.9.3
21.01.2018

### <a name="new"></a>Новинка
* Существующее окно обозревателя службы хранилища будет повторно использоваться в таких случаях:
    * при открытии прямых ссылок, созданных в обозревателе службы хранилища;
    * при открытии обозревателя службы хранилища с портала;
    * при открытии обозревателя службы хранилища из расширения VS Code для службы хранилища Azure (ожидается в ближайшее время).
* Добавлена возможность открывать новое окно обозревателя службы хранилища из обозревателя.
    * В Windows параметр "Новое окно" есть в меню "Файл" и в контекстном меню на панели задач.
    * В Mac параметр New Window (Новое окно) включен в меню приложения.

### <a name="fixes"></a>Исправления
* Исправлена проблема с безопасностью. Выполните обновление до версии 0.9.4 при первой возможности.
* Устаревшие действия удалялись неправильно. Это влияло на производительность длительных заданий. Теперь они удаляются правильно.
* Действия с большим количеством файлов и каталогов иногда приводили к "зависанию" обозревателя службы хранилища. Теперь можно регулировать запросы в Azure для файловых ресурсов, чтобы ограничить использование системных ресурсов.

### <a name="known-issues"></a>Известные проблемы
* Обозреватель хранилищ не поддерживает учетные записи AD FS.
* Сочетания клавиш для команд "View Explorer" (Просмотреть обозреватель) и "View Account Management" (Просмотреть управление учетными записями" должны быть CTRL или CMD+SHIFT+E и CTRL или CMD+SHIFT+A соответственно.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный здесь обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* Панель "Параметры учетной записи" может подать запрос на повторный ввод учетных данных для фильтрации подписок.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* Несмотря на то, что сейчас Azure Stack не поддерживает общие файловые ресурсы, соответствующий узел все равно отображается под подключенной учетной записью хранения Azure Stack.
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer --disable-gpu
    ```

* Пользователям Ubuntu 14.04 понадобится обновить GCC. Для этого нужно выполнить команды ниже, а затем перезагрузить компьютер:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Пользователям Ubuntu 17.04 понадобится установить GConf. Для этого нужно выполнить следующие команды, а затем перезагрузить компьютер:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>Версия 0.9.2
11/01/2017

### <a name="hotfixes"></a>Исправления
* При редактировании значений Edm.DateTime для сущностей таблицы могли произойти непредвиденные изменения данных в зависимости от локального часового пояса. Теперь редактор использует обычное текстовое поле, предоставляя точный, согласованный контроль над значениями Edm.DateTime.
* Если группа больших двоичных объектов имеет присоединенные ключ или имя, их передача или загрузка не начиналась. Теперь эта проблема устранена.
* Раньше обозреватель службы хранилища запрашивал повторную проверку подлинности устаревшей учетной записи, если было выбрано одну или несколько подписок учетной записи. Теперь же он запрашивает повторную проверку подлинности, даже если учетная запись полностью отфильтрована.
* Домен конечных точек для Azure для государственных организаций США был неправильным. Эта ошибка исправлена.
* Иногда не нажималась кнопка "Применить" на панели управления учетными записями. Такого больше не случится.

### <a name="new"></a>Новинка
* Поддержка предварительной версии для Azure Cosmos DB:
    * [электронная документация](./cosmos-db/storage-explorer.md);
    * создание баз данных и коллекций;
    * Обработка данных
    * запрос, создание и удаление документов;
    * обновление хранимых процедур, определяемых пользователем функций или триггеров;
    * использование строк подключения для подключения к базам данных и управления ими.
* Повышена производительность при передаче и скачивании множества больших двоичных объектов небольшого размера.
* Добавлено действие "Повторить все" в случае сбоев в группе передачи больших двоичных объектов или группе скачивания больших двоичных объектов.
* Теперь обозреватель хранилищ будет приостанавливать итерацию во время передачи или скачивания больших двоичных объектов, если он обнаружит, что сетевое подключение прервано. После восстановления сетевого подключения итерацию можно будет возобновить.
* В контекстное меню добавлены функции "Закрыть все", "Закрыть остальные" и "Закрыть" для вкладок.
* Обозреватель хранилищ теперь использует собственные диалоговые окна и контекстные меню.
* Обозреватель хранилищ стал более удобным. Добавлены некоторые улучшения, среди которых:
    * Улучшена поддержка средства чтения с экрана для NVDA в Windows и для VoiceOver в Mac.
    * Улучшены контрастные темы.
    * Исправлен переход с помощью клавиши TAB и фокус клавиатуры.

### <a name="fixes"></a>Исправления
* Если вы пытались открыть или скачать большой двоичный объект с недопустимым именем файла Windows, операция завершалась сбоем. Теперь обозреватель хранилищ будет определять, является ли недопустимым имя большого двоичного объекта, и предлагать кодировать или пропустить этот большой двоичный объект. Обозреватель хранилищ также определяет, закодировано ли имя файла, предлагает декодировать его перед передачей.
* Во время передачи большого двоичного объекта редактор для целевого контейнера больших двоичных объектов иногда не обновлялся должным образом. Теперь эта проблема устранена.
* Поддержка нескольких видов строк подключения и универсальных кодов ресурса (URI) SAS возвращена. Мы исправили все известные проблемы, но если возникают какие-либо дополнительные проблемы, отправьте нам отзыв.
* У некоторых пользователей в версии 0.9.0 уведомление об обновлении не работало. Эта проблема была устранена. Если у вас возникла эта ошибка, то вы можете вручную скачать последнюю версию обозревателя хранилищ [отсюда](https://azure.microsoft.com/features/storage-explorer/).

### <a name="known-issues"></a>Известные проблемы
* Обозреватель хранилищ не поддерживает учетные записи AD FS.
* Сочетания клавиш для команд "View Explorer" (Просмотреть обозреватель) и "View Account Management" (Просмотреть управление учетными записями" должны быть CTRL или CMD+SHIFT+E и CTRL или CMD+SHIFT+A соответственно.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный здесь обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* Панель "Параметры учетной записи" может подать запрос на повторный ввод учетных данных для фильтрации подписок.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* Несмотря на то, что сейчас Azure Stack не поддерживает общие файловые ресурсы, соответствующий узел все равно отображается под подключенной учетной записью хранения Azure Stack.
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer --disable-gpu
    ```

* Пользователям Ubuntu 14.04 понадобится обновить GCC. Для этого нужно выполнить команды ниже, а затем перезагрузить компьютер:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Пользователям Ubuntu 17.04 понадобится установить GConf. Для этого нужно выполнить следующие команды, а затем перезагрузить компьютер:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>Версии 0.9.1 и 0.9.0
20.10.2017
### <a name="new"></a>Новинка
* Поддержка предварительной версии для Azure Cosmos DB:
    * [электронная документация](./cosmos-db/storage-explorer.md);
    * создание баз данных и коллекций;
    * Обработка данных
    * запрос, создание и удаление документов;
    * обновление хранимых процедур, определяемых пользователем функций или триггеров;
    * использование строк подключения для подключения к базам данных и управления ими.
* Повышена производительность при передаче и скачивании множества больших двоичных объектов небольшого размера.
* Добавлено действие "Повторить все" в случае сбоев в группе передачи больших двоичных объектов или группе скачивания больших двоичных объектов.
* Теперь обозреватель хранилищ будет приостанавливать итерацию во время передачи или скачивания больших двоичных объектов, если он обнаружит, что сетевое подключение прервано. После восстановления сетевого подключения итерацию можно будет возобновить.
* В контекстное меню добавлены функции "Закрыть все", "Закрыть остальные" и "Закрыть" для вкладок.
* Обозреватель хранилищ теперь использует собственные диалоговые окна и контекстные меню.
* Обозреватель хранилищ стал более удобным. Добавлены некоторые улучшения, среди которых:
    * Улучшена поддержка средства чтения с экрана для NVDA в Windows и для VoiceOver в Mac.
    * Улучшены контрастные темы.
    * Исправлен переход с помощью клавиши TAB и фокус клавиатуры.

### <a name="fixes"></a>Исправления
* Если вы пытались открыть или скачать большой двоичный объект с недопустимым именем файла Windows, операция завершалась сбоем. Теперь обозреватель хранилищ будет определять, является ли недопустимым имя большого двоичного объекта, и предлагать кодировать или пропустить этот большой двоичный объект. Обозреватель хранилищ также определяет, закодировано ли имя файла, предлагает декодировать его перед передачей.
* Во время передачи большого двоичного объекта редактор для целевого контейнера больших двоичных объектов иногда не обновлялся должным образом. Теперь эта проблема устранена.
* Поддержка нескольких видов строк подключения и универсальных кодов ресурса (URI) SAS возвращена. Мы исправили все известные проблемы, но если возникают какие-либо дополнительные проблемы, отправьте нам отзыв.
* У некоторых пользователей в версии 0.9.0 уведомление об обновлении не работало. Эта проблема была устранена. Если у вас возникла эта ошибка, то вы можете вручную скачать последнюю версию обозревателя хранилищ [отсюда](https://azure.microsoft.com/features/storage-explorer/).

### <a name="known-issues"></a>Известные проблемы
* Обозреватель хранилищ не поддерживает учетные записи AD FS.
* Сочетания клавиш для команд "View Explorer" (Просмотреть обозреватель) и "View Account Management" (Просмотреть управление учетными записями" должны быть CTRL или CMD+SHIFT+E и CTRL или CMD+SHIFT+A соответственно.
* При нацеливании на Azure Stack передача определенных файлов в качестве добавляемых больших двоичных объектов может завершиться ошибкой.
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Причина заключается в том, что мы используем описанный здесь обходной путь для отмены фильтра.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* Панель "Параметры учетной записи" может подать запрос на повторный ввод учетных данных для фильтрации подписок.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* Несмотря на то, что сейчас Azure Stack не поддерживает общие файловые ресурсы, соответствующий узел все равно отображается под подключенной учетной записью хранения Azure Stack.
* Оболочка Electron, используемая обозревателем хранилищ, испытывает проблемы с аппаратным ускорением в некоторых GPU (графический процессор). Если обозреватель хранилищ отображает пустое главное окно, можно попробовать запустить обозреватель хранилищ из командной строки и отключить ускорение GPU, добавив параметр `--disable-gpu`.

    ```
    ./StorageExplorer --disable-gpu
    ```

* Пользователям Ubuntu 14.04 понадобится обновить GCC. Для этого нужно выполнить команды ниже, а затем перезагрузить компьютер:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Пользователям Ubuntu 17.04 понадобится установить GConf. Для этого нужно выполнить следующие команды, а затем перезагрузить компьютер:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>Версия 0.8.16
8/21/2017

### <a name="new"></a>Новинка
* Если при открытии большого двоичного объекта обнаружено изменение, в обозревателе хранилищ отображается запрос на передачу скачанного файла.
* Улучшена процедура входа в Azure Stack.
* Повышена производительность при одновременной передаче и загрузке множества небольших файлов.


### <a name="fixes"></a>Исправления
* Выбор замены некоторых типов больших двоичных объектов во время конфликта передачи в некоторых случаях приводил к перезапуску передачи.
* В версии 0.8.15 процесс передачи мог остановиться на 99 %.
* Если при передаче файлов в общую папку выбирался несуществующий каталог, передача завершалась сбоем.
* Обозреватель хранилищ неправильно создавал метки времени для подписанных URL-адресов и запросов к таблицам.


### <a name="known-issues"></a>Известные проблемы
* В настоящее время строка подключения с использованием имени и ключа не работает. Эта проблема будет устранена в следующем выпуске. До этого времени вы можете использовать подключение с именем и ключом.
* При попытке открыть файл с недопустимым для Windows именем скачивание завершится ошибкой "Файл не найден".
* После нажатия кнопки "Отмена" задача завершается через некоторое время. Это ограничение библиотеки узлов службы хранилища Azure.
* После завершения передачи большого двоичного объекта, вкладка, с помощью которой инициировано соответствующее действие, обновляется. Это отличается от предыдущего поведения. Кроме того, после этого вы перейдете в корень используемого контейнера.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобится перезапустить обозреватель хранилищ, чтобы он не сохранял ошибочные данные.
* Панель "Параметры учетной записи" может подать запрос на повторный ввод учетных данных для фильтрации подписок.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* Несмотря на то, что сейчас Azure Stack не поддерживает общие файловые ресурсы, соответствующий узел все равно отображается под подключенной учетной записью хранения Azure Stack.
* Пользователям Ubuntu 14.04 понадобится обновить GCC. Для этого нужно выполнить команды ниже, а затем перезагрузить компьютер:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Пользователям Ubuntu 17.04 понадобится установить GConf. Для этого нужно выполнить следующие команды, а затем перезагрузить компьютер:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Версия 0.8.14
22.06.2017

### <a name="new"></a>Новинка

* Версия Electron обновлена до 1.7.2, чтобы воспользоваться преимуществами нескольких критически важных обновлений.
* Теперь вы можете быстро получить доступ к электронному руководству по устранению неполадок в меню "Справка".
* Storage Explorer Troubleshooting [Guide][2]
* [Instructions][3] on connecting to an Azure Stack subscription

### <a name="known-issues"></a>Известные проблемы

* Кнопки в диалоговом окне подтверждения удаления папки не регистрируются щелчками мыши в Linux. Решение — использовать клавишу ВВОД.
* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобиться перезапустить обозреватель хранилищ, чтобы он забыл это решение.
* При одновременной передаче более трех групп больших двоичных объектов или файлов могут возникнуть ошибки.
* Панель "Параметры учетной записи" может запросить повторный ввод учетных данных для фильтрации подписок.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* Несмотря на то, что сейчас Azure Stack не поддерживает общие файловые ресурсы, соответствующий узел все равно отображается под подключенной учетной записью хранения Azure Stack.
* Для установки Ubuntu 14.04 требуется обновить версию gcc (соответствующие команды приведены ниже):

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Версия 0.8.13
05/12/2017

#### <a name="new"></a>Новинка

* Storage Explorer Troubleshooting [Guide][2]
* [Instructions][3] on connecting to an Azure Stack subscription

#### <a name="fixes"></a>Исправления

* Исправлено: при отправке файлов с высокой вероятностью могла произойти ошибка нехватки памяти.
* Исправлено: теперь можно входить с использованием ПИН-кода или смарт-карты.
* Fixed: Open in Portal now works with Azure China 21Vianet, Azure Germany, Azure US Government, and Azure Stack
* Исправлено: иногда при передаче папки в контейнер большого двоичного объекта возникала ошибка "Недопустимая операция".
* Исправлено: функция "Выбрать все" отключалась при управлении моментальными снимками.
* Исправлено: метаданные базового большого двоичного объекта могли перезаписаться после просмотра свойств его моментальных снимков.

#### <a name="known-issues"></a>Известные проблемы

* Если выбран неправильный ПИН-код или сертификат смарт-карты, понадобиться перезапустить обозреватель хранилищ, чтобы он забыл это решение.
* При увеличении и уменьшении уровень масштаба может моментально измениться до уровня по умолчанию.
* При одновременной передаче более трех групп больших двоичных объектов или файлов могут возникнуть ошибки.
* Панель "Параметры учетной записи" может запросить повторный ввод учетных данных для фильтрации подписок.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* Несмотря на то, что сейчас Azure Stack не поддерживает общие файловые ресурсы, соответствующий узел все равно отображается под подключенной учетной записью хранения Azure Stack.
* Для установки Ubuntu 14.04 требуется обновить версию gcc (соответствующие команды приведены ниже):

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Версии 0.8.12, 0.8.11 и 0.8.10
07.04.2017

#### <a name="new"></a>Новинка

* Обозреватель хранилищ автоматически закрывается при установке обновления с использованием уведомления об обновлении.
* Быстрый доступ обеспечивает дополнительные возможности для работы с часто используемыми ресурсами.
* В редакторе контейнера больших двоичных объектов можно узнать, к какой виртуальной машине принадлежит арендованный большой двоичный объект.
* Левую панель можно свернуть.
* Обнаружение и скачивание выполняются одновременно.
* С помощью функции получения статистики в контейнерах больших двоичных объектов, общих файловых ресурсах и редакторах таблиц можно увидеть размер ресурса или выделенного элемента.
* Теперь можно войти в учетные записи Azure Stack на основе Azure Active Directory (AAD).
* Теперь можно отправить архивные файлы более 32 МБ в учетные записи хранения класса Premium.
* Улучшена поддержка специальных возможностей.
* Теперь можно добавить надежный SSL-сертификат X.509 в кодировке Base-64 (выберите "Редактирование -&gt; SSL-сертификаты -&gt; Импорт сертификатов").

#### <a name="fixes"></a>Исправления

* Исправлено: иногда после обновления учетных данных учетной записи представление в виде дерева не обновлялось автоматически.
* Исправлено: при создании SAS для очередей и таблиц эмулятора приводило к тому, что URL-адрес становился недопустимым.
* Исправлено: учетные записи хранения уровня Premium можно развернуть, пока включен прокси-сервер.
* Исправлена: кнопка "Применить" на странице управления учетными записями не работала, если выбрана одна учетная запись или они не выбраны вообще.
* Исправлено (в версии 0.8.11): происходил сбой при отправке больших двоичных объектов, требующих разрешения конфликтов.
* Исправлено (в версии 0.8.12): отправка отзывов не работала в версии 0.8.11.

#### <a name="known-issues"></a>Известные проблемы

* После обновления до версии 0.8.10 необходимо обновить все учетные данные.
* При увеличении и уменьшении уровень масштаба может моментально измениться до уровня по умолчанию.
* При одновременной передаче более трех групп больших двоичных объектов или файлов могут возникнуть ошибки.
* Панель "Параметры учетной записи" может запросить повторный ввод учетных данных для фильтрации подписок.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* Несмотря на то, что сейчас Azure Stack не поддерживает общие файловые ресурсы, соответствующий узел все равно отображается под подключенной учетной записью хранения Azure Stack.
* Для установки Ubuntu 14.04 требуется обновить версию gcc (соответствующие команды приведены ниже):

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089-and-088"></a>Версии 0.8.9 и 0.8.8
23.02.2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>Новинка

* Обозреватель хранилищ версии 0.8.9 автоматически скачает последнюю версию обновления.
* Исправление: использование созданного с помощью портала URI SAS для подключения к учетной записи хранения приводило к ошибке.
* Теперь можно создавать и повышать уровень моментальные снимков больших двоичных объектов, а также управлять ими.
* Вы можете войти в учетные записи Azure для Китая (21Vianet), Германии и государственных организаций США.
* Теперь можно изменить уровень масштаба. Используйте параметры в меню "Вид", чтобы увеличить, уменьшить и сбросить масштаб.
* Символы Юникода поддерживаются в метаданных пользователя для больших двоичных объектов и файлов.
* Улучшены специальные возможности.
* Заметки о выпуске следующей версии можно просмотреть в уведомлении об обновлении. Заметки о текущем выпуске можно просмотреть в меню "Справка".

#### <a name="fixes"></a>Исправления

* Исправлено: номер версии правильно отображается на панели управления в Windows.
* Исправлено: поиск больше не ограничен 50 000 узлами.
* Исправлено: отправка в общий файловый ресурс не заканчивалась, если целевого каталога не существовало.
* Исправлено: повышена стабильность при длительной передаче и скачивании.

#### <a name="known-issues"></a>Известные проблемы

* При увеличении и уменьшении уровень масштаба может моментально измениться до уровня по умолчанию.
* Быстрый доступ работает только с элементами подписки. В этом выпуске не поддерживаются локальные ресурсы или ресурсы, подключенные с помощью ключа или маркера SAS.
* Чтобы перейти к целевому ресурсу с помощью быстрого доступа, может потребоваться несколько секунд. Это зависит от количества ваших ресурсов.
* При одновременной передаче более трех групп больших двоичных объектов или файлов могут возникнуть ошибки.

12/16/2016
### <a name="version-087"></a>Версия 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Новинка

* Вы можете выбрать способ разрешения конфликтов в начале сеанса обновления, скачивания или копирования в окне "Действия".
* Наведите указатель на вкладку, чтобы увидеть полный путь к ресурсу службы хранилища.
* При выборе вкладки она синхронизируется с расположением в левой части области навигации.

#### <a name="fixes"></a>Исправления

* Исправлено: обозреватель хранилищ теперь относится к доверенным приложениям в Mac.
* Исправлено: ОС Ubuntu 14.04 снова поддерживается.
* Исправлено: иногда элемент пользовательского интерфейса "Добавление учетной записи" мигал при загрузке подписок.
* Исправлено: иногда не все ресурсы службы хранилища указывались в левой части области навигации.
* Исправлено: иногда на панели операций отображались пустые действия.
* Исправлено: теперь сохраняется размер окна в предыдущем закрытом сеансе.
* Исправлено: возможность открыть несколько вкладок для одного ресурса с помощью контекстного меню.

#### <a name="known-issues"></a>Известные проблемы

* Быстрый доступ работает только с элементами подписки. В этом выпуске не поддерживаются локальные ресурсы или ресурсы, подключенные с помощью ключа или маркера SAS.
* Чтобы перейти к целевому ресурсу с помощью быстрого доступа, может потребоваться несколько секунд. Это зависит от количества ваших ресурсов.
* При одновременной передаче более трех групп больших двоичных объектов или файлов могут возникнуть ошибки.
* Служба поиска обрабатывает поисковые запросы приблизительно по 50 000 узлам. После достижения этого порога может снизиться производительность или могут возникать необработанные исключения.
* При первом использовании Storage Explorer в macOS может появиться несколько запросов, предлагающих указать разрешение пользователя на доступ к цепочке ключей. Мы рекомендуем выбрать "Всегда разрешать", чтобы эти запросы больше не появлялись.

18.11.2016
### <a name="version-086"></a>Версия 0.8.6

#### <a name="new"></a>Новинка

* Теперь вы можете закрепить наиболее часто используемые службы в области быстрого доступа для упрощения навигации.
* Теперь можно открыть несколько редакторов на разных вкладках. Single click to open a temporary tab; double click to open a permanent tab. You can also click on the temporary tab to make it a permanent tab
* Значительное улучшение производительности и стабильности при передаче и скачивании, особенно заметное для больших файлов на быстрых компьютерах.
* Теперь в контейнерах больших двоичных объектов можно создавать пустые "виртуальные" папки.
* Мы повторно представляем поиск в заданных областях с расширенным поиском в подстроках. Теперь у вас есть два варианта поиска:
    * Глобальный поиск. Просто введите условие поиска в текстовое поле поиска.
    * Поиск в области. Щелкните значок лупы рядом с узлом, добавьте условие поиска в конце пути или щелкните правой кнопкой мыши и выберите команду "Искать от этого места".
* Мы добавили различные темы: "Светлая" (по умолчанию), "Темная", "Высокая контрастность черного" и "Высокая контрастность белого". Выберите "Изменить -&gt; Темы", чтобы изменить параметры темы.
* Вы можете изменять свойства больших двоичных объектов и файлов.
* Добавлена поддержка кодированных (base64) и не кодированных сообщений очереди.
* В среде Linux теперь является обязательной 64-разрядная операционная система. Для этого выпуска поддерживается только 64-разрядная Ubuntu 16.04.1 LTS.
* Мы обновили наш логотип!

#### <a name="fixes"></a>Исправления

* Исправлено. Проблема с блокированием экрана.
* Исправлен. Усилена безопасность.
* Исправлено: иногда возникали повторяющиеся подключенные учетные записи.
* Исправлено: большой двоичный объект с содержимым неопределенного типа мог создать исключение.
* Исправлено: не удавалось открыть панель запроса в пустой таблице.
* Исправлено: различные ошибок в поиске.
* Исправлено: увеличено количество загружаемых ресурсов (с 50 до 100) при щелчке "Загрузить еще".
* Исправлено: если при первом запуске выполнен вход в учетную запись, выбираются все подписки для этой учетной записи по умолчанию.

#### <a name="known-issues"></a>Известные проблемы

* Этот выпуск обозревателя хранилища не запускается в Ubuntu 14.04.
* Чтобы открыть несколько вкладок для одного ресурса, не нужно щелкать его несколько раз. Щелкните другой ресурс, вернитесь и щелкните исходный ресурс, чтобы открыть его снова на другой вкладке
* Быстрый доступ работает только с элементами подписки. В этом выпуске не поддерживаются локальные ресурсы или ресурсы, подключенные с помощью ключа или маркера SAS.
* Чтобы перейти к целевому ресурсу с помощью быстрого доступа, может потребоваться несколько секунд. Это зависит от количества ваших ресурсов.
* При одновременной передаче более трех групп больших двоичных объектов или файлов могут возникнуть ошибки.
* Служба поиска обрабатывает поисковые запросы приблизительно по 50 000 узлам. После достижения этого порога может снизиться производительность или могут возникать необработанные исключения.

10/03/2016
### <a name="version-085"></a>Версия 0.8.5

#### <a name="new"></a>Новинка

* Теперь созданные на портале ключи SAS можно использовать для подключения к учетным записям хранения и ресурсам.

#### <a name="fixes"></a>Исправления

* Исправлено: из-за состояния гонки во время поиска иногда узлы невозможно было раскрыть.
* Исправлено: функция "Использовать HTTP" не работала при подключении к учетным записям хранения с использованием имени и ключа учетной записи.
* Исправлено: ключи SAS (созданные на портале) возвращали ошибку с сообщением о косой черте в конце.
* Исправлено: ошибки импорта таблиц.
    * Иногда ключ раздела и строки стояли в обратном порядке.
    * Не удавалось считать ключ раздела со значением null.

#### <a name="known-issues"></a>Известные проблемы

* Служба поиска обрабатывает поисковые запросы приблизительно по 50 000 узлам. После достижения этого порога может снизиться производительность.
* Azure Stack в настоящее время не поддерживает файлы, поэтому попытка развернуть соответствующий узел приведет к ошибке.

12.09.2016
### <a name="version-084"></a>Версия 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Новинка

* Создавайте прямые ссылки на учетные записи хранения, контейнеры, очереди, таблицы или общие файловые ресурсы для упрощения общего доступа к ресурсам (поддерживается в Windows и Mac OS).
* Поиск контейнеров больших двоичных объектов, таблиц, очередей, файловых ресурсов или учетных записей хранения с помощью поля поиска.
* Теперь предложения можно группировать в конструкторе запросов таблицы.
* Переименовывайте, копируйте и вставляйте контейнеры и папки больших двоичных объектов, общие файловые ресурсы, таблицы, большие двоичные объекты, файлы и каталоги из учетных записей и контейнеров, подключенных с использованием SAS.
* Теперь при переименовывании и копировании контейнеров больших двоичных объектов и общих файловых ресурсов сохраняются свойства и метаданные.

#### <a name="fixes"></a>Исправления

* Исправлено: отсутствовала возможность изменения сущностей таблицы с логическими или двоичными свойствами.

#### <a name="known-issues"></a>Известные проблемы

* Служба поиска обрабатывает поисковые запросы приблизительно по 50 000 узлам. После достижения этого порога может снизиться производительность.

08/03/2016
### <a name="version-083"></a>Версия 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Новинка

* Переименование контейнеров, таблиц, файловых ресурсов.
* Улучшена работа с конструктором запросов.
* Возможность сохранять и загружать запросы.
* Прямые ссылки на учетные записи хранения либо контейнеры, очереди, таблицы или файловые ресурсы для совместного использования и удобного доступа к ресурсам (только для Windows, поддержка в macOS будет реализована позже).
* Возможность управлять правилами CORS и настраивать их.

#### <a name="fixes"></a>Исправления

* Исправлено: учетные записи Майкрософт требуют повторной аутентификации каждые 8–12 часов.

#### <a name="known-issues"></a>Известные проблемы

* Иногда пользовательский интерфейс может зависнуть. Разворачивание окна помогает устранить эту проблему.
* Для установки macOS может потребоваться повышенный уровень разрешений.
* Панель "Параметры учетной записи" может запросить повторный ввод учетных данных для фильтрации подписок.
* При переименовывании файловых ресурсов, контейнеров больших двоичных объектов и таблиц не сохраняются метаданные или другие свойства в контейнере, такие как квоты для общего файлового ресурса, уровень общего доступа или политики доступа.
* При переименовании больших двоичных объектов (по отдельности или в переименованном контейнере больших двоичных объектов) не сохраняются моментальные снимки. Все прочие свойства и метаданные больших двоичных объектов, файлов и сущностей при переименовании сохраняются.
* Копирование и переименование ресурсов не работает в учетных записях, подключенных с использованием SAS.

07.07.2016
### <a name="version-082"></a>Версия 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Новинка

* Учетные записи хранения группируются по подпискам. Хранилище разработки и ресурсы, подключенные с использованием ключа или SAS, отображаются в узле (локальном и подключенном).
* Выход из учетных записей осуществляется на панели Azure Account Settings (Параметры учетной записи Azure).
* Настраивайте параметры прокси-сервера для включения входа и управления им.
* Создавайте и прерывайте аренду большого двоичного объекта.
* Открывайте контейнеры больших двоичных объектов, очереди, таблицы и файлы одним щелчком.

#### <a name="fixes"></a>Исправления

* Исправлено: декодировка (base64) сообщений очереди, вставленных с помощью библиотек .NET или Java, выполнялась неправильно.
* Исправлено: таблицы $metrics не отображались для учетных записей хранения больших двоичных объектов.
* Исправлено: узел таблицы не работает в локальном хранилище (разработка).

#### <a name="known-issues"></a>Известные проблемы

* Для установки macOS может потребоваться повышенный уровень разрешений.

15.06.2016
### <a name="version-080"></a>Версия 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Новинка

* Поддержка общих файловых ресурсов: просмотр, передача, скачивание, копирование файлов, каталогов и URI SAS (создание и подключение).
* Оптимизирован процесс подключения к службе хранилища с использованием URI SAS или ключей учетной записи.
* Экспорт результатов запросов таблицы.
* Изменение порядка и настройка столбцов таблицы.
* Просмотр контейнеров больших двоичных объектов $logs и таблиц $metrics для учетных записей хранения с включенными метриками.
* Улучшен экспорт и импорт (теперь включает тип значения свойства).

#### <a name="fixes"></a>Исправления

* Исправлено: передаются или скачиваются не все большие двоичные объекты.
* Исправлено: при изменении, добавлении или импорте сущности со значением числовой строки ("1") тип указанного значения меняется на double.
* Исправлено: не удается развернуть узел таблицы в локальной среде разработки.

#### <a name="known-issues"></a>Известные проблемы

* Таблицы $metrics не отображаются для учетных записей хранения больших двоичных объектов.
* Программно добавленные сообщения очереди могут отображаться неправильно, если они закодированы с использованием кодировки Base64.

17.05.2016
### <a name="version-07201605090"></a>Версия 0.7.20160509.0

#### <a name="new"></a>Новинка

* Улучшена обработка ошибок при сбое приложения.

#### <a name="fixes"></a>Исправления

* Исправлена ошибка, при возникновении которой иногда при запросе учетных данных для входа на информационной панели не появляются сообщения.

#### <a name="known-issues"></a>Известные проблемы

* Таблицы: если при добавлении, изменении и импорте сущности со свойством с неоднозначным числовым значением, например 1 или 1.0, пользователь попытается отправить его как `Edm.String`, значение будет возвращено через клиентский API как Edm.Double.

31.03.2016

### <a name="version-07201603250"></a>Версия 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Новинка

* Поддержка таблиц: просмотр, поиск, экспорт, импорт и выполнение операций CRUD для сущностей.
* Поддержка очередей: просмотр, добавление, вывод сообщений из очереди.
* Создание URI SAS для учетных записей хранения.
* Подключение к учетным записям хранения с использованием URI SAS.
* Уведомления об обновлениях для последующих обновлений обозревателя хранилищ.
* Обновленный интерфейс.

#### <a name="fixes"></a>Исправления

* Улучшения производительности и надежности.

### <a name="known-issues-amp-mitigations"></a>Известные проблемы и способы их устранения

* Скачивание больших файлов большого двоичного объекта выполняется неправильно. Мы рекомендуем использовать AzCopy, пока мы не устраним эту проблему.
* Учетные данные учетной записи невозможно получить или кэшировать, если домашняя папка не найдена или недоступна для записи.
* Если при добавлении, изменении и импорте сущности со свойством с неоднозначным числовым значением, например 1 или 1.0, пользователь попытается отправить его как `Edm.String`, значение будет возвращено через клиентский API как Edm.Double.
* При импорте CSV-файлов с многострочными записями данные могут усекаться или зашифровываться.

03.02.2016

### <a name="version-07201601291"></a>Версия 0.7.20160129.1

#### <a name="fixes"></a>Исправления

* Улучшена производительность при передаче, скачивании и копировании больших двоичных объектов.

14.01.2016

### <a name="version-07201601050"></a>Версия 0.7.20160105.0

#### <a name="new"></a>Новинка

* Поддержка Linux (функции с контролем четности для OS X).
* Добавление контейнеров больших двоичных объектов с использованием подписанного URL-адреса (SAS).
* Добавление учетных записей хранения в Azure для Китая (21Vianet).
* Добавление учетные записей хранения с применением пользовательских конечных точек.
* Открытие и просмотр содержимого больших двоичных объектах с текстом и изображениями.
* Просмотр и изменение свойств и метаданных больших двоичных объектов.

#### <a name="fixes"></a>Исправления

* Исправлено: иногда при отправке или скачивании большого количества больших двоичных объектов (более 500) экран приложения может стать белым.
* Исправлено: при задании уровня общего доступа контейнера больших двоичных объектов новое значение не обновляется до повторной установки фокуса на контейнер. Кроме того, по умолчанию в диалоговом окне всегда отображается значение No public access (Без общего доступа), а не фактическое текущее значение.
* Улучшена работа с клавиатурой, специальные возможности и поддержка пользовательского интерфейса.
* Длинный текст журнала строки навигации переносится с помощью пробела.
* Диалоговое окно SAS поддерживает проверку входных данных.
* Локальное хранилище доступно, даже если истек срок действия учетных данных пользователя.
* При удалении открытого контейнера больших двоичных объектов обозреватель больших двоичных объектов справа закрывается.

#### <a name="known-issues"></a>Известные проблемы

* Для установки Linux требуется обновить версию gcc (соответствующие команды приведены ниже):
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

18.11.2015
### <a name="version-07201511160"></a>Версия 0.7.20151116.0

#### <a name="new"></a>Новинка

* Версии macOS и Windows.
* Входите для просмотра учетных записей хранения. Используйте рабочую учетную запись, учетную запись Майкрософт, двухфакторную проверку подлинности и т. д.
* Локальное хранилище для разработки (используйте эмулятор хранения, только Windows).
* Поддержка ресурсов Azure Resource Manager и классического хранилища.
* Создавайте и удаляйте большие двоичные объекты, очереди и таблицы.
* Ищите конкретные большие двоичные объекты, очереди и таблицы.
* Исследуйте содержимое контейнеров больших двоичных объектов.
* Просматривайте каталоги и перемещайтесь по ним.
* Передавайте, скачивайте и удаляйте большие двоичные объекты и папки.
* Просмотр и изменение свойств и метаданных больших двоичных объектов.
* Создавайте ключи SAS.
* Создавайте хранимые политики доступа (SAP) и управляйте ими.
* Поиск больших двоичных объектов по префиксу.
* Перетаскивайте файлы для отправки или скачивания.

#### <a name="known-issues"></a>Известные проблемы

* При задании уровня общего доступа контейнера больших двоичных объектов новое значение не обновляется до повторной установки фокуса на контейнер.
* При открытии диалогового окна для установки уровня общего доступа постоянно по умолчанию отображается значение No public access (Без общего доступа), а не фактическое текущее значение.
* Не удается переименовать скачанные большие двоичные объекты.
* Иногда при возникновении ошибки записи в журнале действий зависают в состоянии "Выполняется", а ошибка не отображается.
* Иногда при попытке отправки или скачивания большого количества больших двоичных объектов происходит сбой или экран становится белым.
* Иногда отмена операции копирования не работает.
* Если при создании контейнера (больших двоичных объектов, очереди или таблицы) ввести недопустимое имя и приступить к созданию следующего контейнера другого типа, невозможно установить фокус на этот контейнер.
* Не удается создать или переименовать папку.




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
