---
title: включить файл
description: включить файл
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b2ff542d2782293e89b66e5d25cb67a9bcde6da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75773014"
---
Эта ошибка может возникать всякий раз, когда служба "Синхронизация файлов Azure" недоступна с сервера. Вы можете устранить эту ошибку, выполнив следующие шаги:

1. Проверьте, чтобы брандмауэр не блокировал файл `FileSyncSvc.exe` службы Windows.
2. Проверьте, чтобы для исходящих подключений к службе "Синхронизация файлов Azure" был открыт порт 443. Это можно сделать с помощью командлета `Test-NetConnection`. URL-адрес для заполнителя `<azure-file-sync-endpoint>` ниже можно найти в документе о [прокси-сервере службы "Синхронизация файлов Azure" и параметрах брандмауэра](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall). 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Проверьте, чтобы конфигурация прокси-сервера была настроена соответствующим образом. Это можно сделать с помощью командлета `Get-StorageSyncProxyConfiguration`. Дополнительные сведения о настройке конфигурации прокси-сервера для службы "Синхронизация файлов Azure" можно найти в документе о [параметрах прокси-сервера и брандмауэра для этой службы](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. Используйте смдlet Test-StorageNetworkConnectivity для проверки подключения сети к конечным точкам службы. Чтобы узнать больше, смотрите [тестовое подключение сети к конечным точкам обслуживания.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints)    

5. Обратитесь к администратору сети за дополнительной помощью в устранении неполадок подключения к сети.
