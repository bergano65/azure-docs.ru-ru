---
title: Устранение неполадок Azure Data Box при использовании интерфейса REST Документы Майкрософт
description: Описывает, как устранить проблемы, наблюдаемые в azure Data Box, когда копия данных проходит через интерфейс REST.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 7fe5afbc4984c430cbf393e4e2b44122bdd43983
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297127"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Проблемы устранения неполадок, связанные с хранением Azure Data Box Blob

В этой статье подробно описывается информация о том, как устранить проблемы, связанные с устранением неполадок, которые можно увидеть при использовании хранилища Data Box Blob через интерфейс REST на вашем ящике данных для копирования данных. Эти проблемы возникают при использовании хранилища Data Box Blob с другими приложениями или клиентскими библиотеками, такими как Azure Storage Explorer, AzCopy или Azure Storage library for Python.

## <a name="errors-seen-in-azure-storage-explorer"></a>Ошибки, замеченные в Azure Storage Explorer

В этом разделе подробно описаны некоторые проблемы, с которыми сталкиваются при использовании Azure Storage Explorer с хранилищем Data Box Blob.

|Сообщение об ошибке  |Рекомендуемое действие |
|---------|---------|
|Невозможно получить ресурсы ребенка. Значение одного из заголовков HTTP имеет неправильный формат.|Из меню **Edit** выберите **apIs Target Azure Stack.** <br>Перезапустите Azure Storage Explorer.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Убедитесь, что `<accountname>.blob.<serialnumber>.microsoftdatabox.com` имя конечных точек добавлено в файл хостов на этом пути: <li>`C:\Windows\System32\drivers\etc\hosts`на Windows, или </li><li> `/etc/hosts`на Linux.</li>|
|Невозможно получить ресурсы ребенка. <br>Подробности: самоподписанный сертификат |Импорт сертификата TLS/SSL для вашего устройства в Azure Storage Explorer: <li>Скачать сертификат с портала Azure. Для получения дополнительной информации перейдите [на загрузку сертификата](data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>Из меню **Edit** выберите **сертификаты SSL,** а затем выберите **импортные сертификаты.**</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Ошибки, замеченные в AzCopy для Windows

В этом разделе подробно описаны некоторые проблемы, с которыми сталкиваются при использовании AzCopy для Windows с хранилищем Box Blob.

|Сообщение об ошибке  |Рекомендуемое действие |
|---------|---------|
|Команда AzCopy, кажется, зависает в течение минуты перед отображением этой ошибки: <br>Не удалось перечислить каталог https://... Удаленное имя не может быть решено`<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Убедитесь, что `<accountname>.blob.<serialnumber>.microsoftdatabox.com` имя конечных точек `C:\Windows\System32\drivers\etc\hosts`добавлено в файл хостов по адресу: .|
|Команда AzCopy, кажется, зависает в течение минуты перед отображением этой ошибки: <br>Ошибка разбора исходного положения. Основное соединение было закрыто: не удалось установить доверительные отношения для безопасного канала SSL/TLS.|Импортировать сертификат TLS/SSL для вашего устройства в магазин сертификатов системы. Для получения дополнительной информации перейдите [на загрузку сертификата](data-box-deploy-copy-data-via-rest.md#download-certificate).|


## <a name="errors-seen-in-azcopy-for-linux"></a>Ошибки, замеченные в AzCopy для Linux

В этом разделе подробно описаны некоторые проблемы, с которыми сталкиваются при использовании AzCopy для Linux с хранением Data Box Blob.

|Сообщение об ошибке  |Рекомендуемое действие |
|---------|---------|
|Команда AzCopy, кажется, зависает в течение 20 минут перед отображением этой ошибки: <br>Ошибка разбора исходного положения `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`. Отсутствует такое устройство или адрес|Убедитесь, что `<accountname>.blob.<serialnumber>.microsoftdatabox.com` имя конечных точек `/etc/hosts`добавлено в файл хостов по адресу: .|
|Команда AzCopy, кажется, зависает в течение 20 минут перед отображением этой ошибки: <br>Ошибка разбора исходного положения... Соединение SSL установить не удалось.|Импортировать сертификат TLS/SSL для вашего устройства в магазин сертификатов системы. Для получения дополнительной информации перейдите [на загрузку сертификата](data-box-deploy-copy-data-via-rest.md#download-certificate).|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Ошибки, замеченные в библиотеке хранения Azure для Python

В этом разделе подробно рассматриваются некоторые из основных проблем, которые возникают при развертывании Диска Data Box в случае использования клиента Linux для копирования данных.

|Сообщение об ошибке  |Рекомендуемое действие |
|---------|---------|
|Значение одного из заголовков HTTP имеет неправильный формат. |Установленная версия библиотеки хранения данных Microsoft Azure для Python не поддерживается Data Box. Просмотрите требования к хранению данных Azure Data Box Blob для поддерживаемых версий.|
|… (SSL: CERTIFICATE_VERIFY_FAILED) ...|Перед запуском Python установите переменную среды REQUESTS_CA_BUNDLE на траекторию закодированного файла сертификата TLS Base64 (см., как [загрузить сертификат).](data-box-deploy-copy-data-via-rest.md#download-certificate) <br>Пример:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>Кроме того, добавьте сертификат в хранилище сертификатов системы, а затем установите эту переменную среды в путь этого хранилища. <br> Например, в Ubuntu:  <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Распространенные ошибки

Эти ошибки не являются специфическими для любого приложения.

|Сообщение об ошибке  |Рекомендуемое действие |
|---------|---------|
|Время подключения. |Зарегистрируйтесь в устройстве Data Box и убедитесь, что оно разблокировано. Каждый раз, когда устройство перезапускается, оно остается заблокированным, пока кто-то не впишется.|

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте о [требованиях системы хранения данных Box Blob.](data-box-system-requirements-rest.md)
