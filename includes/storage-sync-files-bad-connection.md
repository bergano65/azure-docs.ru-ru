---
title: включение файла
description: включение файла
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 359347e41264711a6ac0fa4d2dd0c3633590e917
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66159887"
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
    
4. Обратитесь к администратору сети за дополнительной помощью в устранении неполадок подключения к сети.