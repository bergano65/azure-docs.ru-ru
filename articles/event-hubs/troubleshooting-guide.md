---
title: Руководство по устранению неполадок в концентраторах событий Azure | Документация Майкрософт
description: В этой статье приводится список исключений обмена сообщениями Центров событий и предлагаемые действия.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: ab3cbdf938409f4eacffa10ae5cb20f8c36b5856
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124675"
---
# <a name="azure-event-hubs---troubleshooting-guide"></a>Концентраторы событий Azure — Руководство по устранению неполадок
В этой статье содержатся советы и рекомендации по устранению некоторых проблем, которые могут возникнуть при использовании Azure EventHubs.

## <a name="connectivity-certificate-or-timeout-issues"></a>Проблемы с подключением, сертификатом или временем ожидания
Следующие шаги могут помочь при устранении неполадок с подключением, сертификатами и временем ожидания для всех служб в каталоге *. servicebus.windows.net. 

- Перейдите к или [wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/` . Он помогает проверять наличие IP-фильтрации, виртуальной сети или цепочки сертификатов (чаще всего при использовании пакета SDK для Java).

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
- Выполните следующую команду, чтобы проверить, заблокирован ли порт в брандмауэре. Используются порты 443 (HTTPS), 5671 (AMQP) и 9093 (Kafka). В зависимости от используемой библиотеки также используются другие порты. Ниже приведен пример команды, которая проверяет, заблокирован ли порт 5671.

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    В Linux

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- При наличии периодических проблем с подключением выполните следующую команду, чтобы проверить наличие пропущенных пакетов. Эта команда попытается установить 25 разных TCP-подключений каждые 1 секунду со службой. После этого можно проверить, сколько из них прошло успешное выполнение и завершилось сбоем, а также увидеть задержку подключения TCP. Это средство можно загрузить `psping` [отсюда](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    Аналогичные команды можно использовать, если вы используете другие средства, такие как `tnc` , `ping` и т. д. 
- Найдите трассировку сети, если предыдущие шаги не помогают и не анализируют их с помощью таких средств, как [Wireshark](https://www.wireshark.org/). При необходимости обратитесь в [Служба поддержки Майкрософт](https://support.microsoft.com/) . 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Проблемы, которые могут возникнуть при обновлении или перезапуске службы
Обновление и перезапуск серверной службы могут привести к следующим последствиям для приложений:

- Запросы могут быть продолжены с немедленным регулированием.
- Может существовать перетаскивание входящих сообщений или запросов.
- Файл журнала может содержать сообщения об ошибках.
- Приложения могут быть отключены от службы в течение нескольких секунд.

Если код приложения использует пакет SDK, то политика повторных попыток уже встроена и активна. Приложение будет повторно подключено без значительного влияния на приложение или рабочий процесс.


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Центрах событий см. в следующих источниках:

* [Общие сведения о Центрах событий](event-hubs-what-is-event-hubs.md)
* [Создание концентратора событий](event-hubs-create.md)
* [Центры событий: часто задаваемые вопросы](event-hubs-faq.md)
