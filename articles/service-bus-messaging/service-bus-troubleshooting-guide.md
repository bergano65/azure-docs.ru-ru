---
title: Руководство по устранению неполадок для сервисного автобуса Azure (ru) Документы Майкрософт
description: В этой статье приводится список исключений из сообщений Azure Service Bus и предложены действия, которые будут предприняты при возникновении исключения.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: aschhab
ms.openlocfilehash: 63bf035d4e19cc1d64998a6ad533812e71ee71b8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887779"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Руководство по устранению неполадок для сервисного автобуса Azure
В этой статье приведены советы и рекомендации по устранению неполадок для нескольких проблем, которые вы можете увидеть при использовании Azure Service Bus. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Проблемы подключения, сертификата или тайм-аута
Следующие шаги могут помочь вам с устранением неполадок подключения / сертификат / тайм-аут вопросы для всех услуг в соответствии с servicebus.windows.net. 

- Просмотрите или [wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/`. Это помогает с проверкой, есть ли у вас IP фильтрации или виртуальной сети или сертификата цепи вопросов (наиболее распространенных при использовании Java SDK).

    Пример успешного сообщения:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Пример сообщения об ошибке сбоя:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Запустите следующую команду, чтобы проверить, заблокирован ли какой-либо порт на брандмауэре. Используемые порты: 443 (HTTPS), 5671 (АМЗП) и 9354 (Чистые сообщения/SBMP). В зависимости от используемой библиотеки используются и другие порты. Вот пример команды, которые проверяют, является ли 5671 порт заблокирован. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    В Linux

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- При периодических проблемах с подключением запустите следующую команду, чтобы проверить, есть ли упаденые пакеты. Эта команда будет пытаться установить 25 различных соединений TCP каждую секунду с службой. Затем вы можете проверить, сколько из них удалось / не удалось, а также увидеть TCP задержки соединения. Вы можете `psping` скачать инструмент [отсюда](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Вы можете использовать эквивалентные команды, если вы `tnc` `ping`используете другие инструменты, такие как , и так далее. 
- Получить сетевой след, если предыдущие шаги не помогают и анализировать его с помощью таких инструментов, как [Wireshark.](https://www.wireshark.org/) При необходимости обратитесь в [службу поддержки майкрософт.](https://support.microsoft.com/) 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Проблемы, возникающие при обновлении/перезагрузке служб
Обновление и перезагрузка бэкэнда может привести к следующему воздействию на приложения:

- Запросы могут быть на мгновение задушен.
- Может быть снижение числа входящих сообщений/запросов.
- Файл журнала может содержать сообщения об ошибках.
- Приложения могут быть отключены от службы на несколько секунд.

Если в коде приложения используется SDK, политика повтора уже встроена и активна. Приложение будет повторно подключаться без существенного влияния на приложение/рабочий процесс.

## <a name="unauthorized-access-send-claims-are-required"></a>Несанкционированный доступ: Требуются претензии к отправке
Вы можете увидеть эту ошибку при попытке получить доступ к теме Service Bus от Visual Studio на предварительном компьютере с помощью установленного пользователем управляемого удостоверения с разрешениями отправки.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

Чтобы устранить эту ошибку, установите библиотеку [Microsoft.Azure.Services.AppAuthentication.](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)  Для получения дополнительной информации [см.](..\key-vault\service-to-service-authentication.md#local-development-authentication) 

Чтобы узнать, как назначить разрешения на роли, см. [Authenticate управляемого идентификатора с помощью Active Directory Azure для доступа к ресурсам Azure Service Bus.](service-bus-managed-service-identity.md)

## <a name="next-steps"></a>Дальнейшие действия
См. следующие статьи: 

- [Исключения менеджера ресурсов Azure](service-bus-resource-manager-exceptions.md). В нем перечисляются исключения, создаваемые при взаимодействии с Azure Service Bus с помощью менеджера ресурсов Azure (через шаблоны или прямые звонки).
- [Исключения для обмена сообщениями](service-bus-messaging-exceptions.md). В нем приводится список исключений, генерируемых системой .NET для сервиса Azure Bus. 

