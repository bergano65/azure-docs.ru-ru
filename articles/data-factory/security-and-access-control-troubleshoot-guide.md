---
title: Устранение проблем безопасности и контроля доступа
description: Узнайте, как устранять проблемы безопасности и контроля доступа в фабрике данных Azure.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 21a1523016502bd7b0a8682461f1fc16acda2ebb
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "96008737"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Устранение неполадок, связанных с управлением безопасностью и доступом в фабрике данных Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье рассматриваются распространенные способы устранения неполадок безопасности и контроля доступа в фабрике данных Azure.

## <a name="common-errors-and-messages"></a>Распространенные ошибки и сообщения

### <a name="invalid-or-empty-authentication-key-issue-after-disabling-public-network-access"></a>Недействительная или пустая ошибка ключа проверки подлинности после отключения доступа к общедоступной сети

#### <a name="symptoms"></a>Симптомы

Локальная среда выполнения интеграции вызывает ошибку "ключ проверки подлинности недопустим или пуст" после отключения доступа к общедоступной сети для фабрики данных.

#### <a name="cause"></a>Причина:

Проблема, скорее всего, вызвана проблемой с разрешением DNS, так как отключение общедоступного подключения и установка частной конечной точки не помогают повторно подключиться.

#### <a name="resolution"></a>Разрешение

1. PsPing полное доменное имя ADF и обнаружило, что буфер был бы общедоступной конечной точкой ADF, даже после отключения.

1. Измените файл узла в виртуальной машине, чтобы подключить частный IP-адрес к полному доменному имени, а затем снова запустите PsPing. Буфер сможет попасть на правильный частный IP-адрес ADF.

1. Повторно зарегистрируйте локальную среду выполнения интеграции, и ее можно будет найти и запустить.
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Не удалось зарегистрировать ключ проверки подлинности IR на самостоятельно размещенных виртуальных машинах из-за частной ссылки

#### <a name="symptoms"></a>Симптомы

Не удалось зарегистрировать ключ проверки подлинности IR на виртуальной машине, размещенной на собственном сервере, так как включена частная ссылка.

Сведения об ошибке показаны ниже:

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>Причина:

Эта ошибка может быть вызвана тем, что виртуальная машина пытается установить шир. Доступ к общедоступной сети должен быть включен для подключения к облаку.

#### <a name="resolution"></a>Разрешение

 **Решение 1.** Чтобы устранить проблему, выполните указанные ниже действия.

1. Перейдите по следующей ссылке: 
    
    https://docs.microsoft.com/rest/api/datafactory/Factories/Update

1. Щелкните " **попробовать** " и заполните все необходимые сведения. Вставьте ниже свойство в **тексте** :

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Нажмите кнопку **выполнить** в конце страницы, чтобы запустить функцию. Убедитесь, что вы получите код ответа 200. Вставленное свойство будет отображаться также в определении JSON.

1. Затем можно снова добавить ключ проверки подлинности в среде выполнения интеграции.


**Решение 2.** Для решения можно обратиться к следующей статье:

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

Попробуйте включить доступ к общедоступной сети с помощью пользовательского интерфейса.

![Включить доступ к общедоступной сети](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Следующие шаги

Для получения дополнительных сведений об устранении неполадок воспользуйтесь следующими ресурсами:

*  [Частная ссылка для фабрики данных](data-factory-private-link.md)
*  [Блог о Фабрике данных](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Запросы на добавление функции в Фабрику данных](https://feedback.azure.com/forums/270578-data-factory)
*  [Видео по Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Страница вопросов (раздел вопросов и ответов на сайте Майкрософт)](/answers/topics/azure-data-factory.html)
*  [Форум о переполнении стека для фабрики данных](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Сведения о Фабрике данных в Twitter](https://twitter.com/hashtag/DataFactory)