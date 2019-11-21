---
title: Troubleshooting guide - Azure DNS
description: In this learning path, get started troubleshooting common issues with Azure DNS
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 09/20/2019
ms.author: allensu
ms.openlocfilehash: b5fedba7b739c07a37f3aabf75ddd8ca465ba73b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74210937"
---
# <a name="azure-dns-troubleshooting-guide"></a>Руководство по устранению неполадок службы DNS Azure

This article provides troubleshooting information for common Azure DNS questions.

Если с помощью описанных выше шагов не удалось решить проблему, то воспользуйтесь поиском или сообщите о свой проблеме на нашем [форуме поддержки сообщества на сайте MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WAVirtualMachinesVirtualNetwork). Or, you can open an Azure support request.


## <a name="i-cant-create-a-dns-zone"></a>Не удается создать зону DNS

Устранить распространенные неполадки можно с помощью одного или нескольких описанных ниже методов.

1.  Просмотрите журналы аудита службы DNS Azure, чтобы определить причину сбоя.
2.  Каждое имя зоны DNS должно быть уникальным в пределах соответствующей группы ресурсов. That is, two DNS zones with the same name can't share a resource group. Попробуйте использовать другое имя зоны или другую группу ресурсов.
3.  Может появиться сообщение об ошибке "Достигнуто или превышено максимально допустимое число зон в подписке {идентификатор подписки}". Используйте другую подписку Azure, удалите несколько зон или обратитесь в службу поддержки Azure, чтобы расширить ограничения для подписки.
4.  Может появиться сообщение об ошибке "Зона "{имя зоны}" недоступна". Эта ошибка означает, что службе DNS Azure не удалось выделить серверы имен для этой зоны DNS. Попробуйте использовать другое имя зоны. Or, if you are the domain name owner you can contact Azure support to allocate name servers for you.


### <a name="recommended-articles"></a>Recommended articles

* [Зоны и записи DNS](dns-zones-records.md)
* [Создание зоны DNS](dns-getstarted-create-dnszone-portal.md)

## <a name="i-cant-create-a-dns-record"></a>Не удается создать запись DNS

Устранить распространенные неполадки можно с помощью одного или нескольких описанных ниже методов.

1.  Просмотрите журналы аудита службы DNS Azure, чтобы определить причину сбоя.
2.  Набор записей уже существует?  Azure DNS управляет записями с помощью *наборов* записей. Это коллекция записей одного типа и с одинаковыми именами. Если запись того же типа и с тем же именем уже существует, для добавления другой такой записи следует изменить существующий набор записей.
3.  Вы пытаетесь создать запись на вершине зоны DNS (в "корне" зоны)? Если это так, в соответствии с DNS-соглашением в качестве имени записи используется символ @. Also note that the DNS standards don't permit CNAME records at the zone apex.
4.  Возник конфликт CNAME?  The DNS standards don't allow a CNAME record with the same name as a record of any other type. Если у вас уже есть запись CNAME, создание записи другого типа с тем же именем завершится ошибкой.  Точно так же не удастся создать запись CNAME, если ее имя совпадает с именем существующей записи другого типа. Чтобы устранить конфликт, удалите другие записи или выберите для записи другое имя.
5.  Достигнуто предельное число наборов записей, разрешенных в зоне DNS? Текущее и максимальное число наборов записей отображаются на портале Azure в разделе "Свойства" для зоны. If you've reached this limit, then either delete some record sets or contact Azure Support to raise your record set limit for this zone, then try again. 


### <a name="recommended-articles"></a>Recommended articles

* [Зоны и записи DNS](dns-zones-records.md)
* [Создание зоны DNS](dns-getstarted-create-dnszone-portal.md)



## <a name="i-cant-resolve-my-dns-record"></a>Не удается разрешить новую запись DNS

Разрешение DNS-имен — это многоэтапный процесс, который может завершиться ошибкой по многим причинам. Приведенные ниже действия помогут вам выяснить, почему не удается разрешить запись DNS в зоне, размещенной в службе DNS Azure.

1.  Проверьте, правильно ли настроены записи DNS в Azure DNS. Просмотрите записи DNS на портале Azure и проверьте, правильно ли указаны имя зоны, имя записи и тип записи.
2.  Проверьте, правильно ли разрешаются записи DNS на DNS-серверах Azure.
    - Если вы создаете запросы DNS на локальном компьютере, могут появляться кэшированные результаты, которые не отражают текущее состояние серверов имен.  Кроме того, в корпоративных сетях часто используются прокси-серверы DNS, препятствующие направлению запросов DNS на определенные серверы имен.  Чтобы избежать этих проблем, используйте веб-службу разрешения имен, например [digwebinterface](https://digwebinterface.com).
    - Обязательно указывайте правильные серверы имен для своей зоны DNS (их можно проверить на портале Azure).
    - Убедитесь, что DNS-имя (полное доменное имя, включающее имя зоны) и тип записи указаны правильно.
3.  Убедитесь, что DNS-имя домена правильно [делегировано DNS-серверам Azure](dns-domain-delegation.md). [Существует множество сторонних веб-сайтов для проверки делегирования DNS](https://www.bing.com/search?q=dns+check+tool). Это тест делегирования *зоны*, поэтому следует вводить только имя зоны DNS, а не полное имя записи.
4.  После выполнения этих шагов запись DNS должна разрешаться правильно. Чтобы проверить, так ли это, можно повторно использовать [digwebinterface](https://digwebinterface.com) — на этот раз с использованием стандартных параметров сервера имен.


### <a name="recommended-articles"></a>Recommended articles

* [Делегирование домена в Azure DNS](dns-domain-delegation.md)



## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>Как указать службу и протокол для записи SRV

Azure DNS управляет записями DNS как наборами записей. Это коллекции записей одного типа и с одинаковыми именами. Для набора записей SRV службу и протокол необходимо указывать как часть имени набора записей. Другие параметры SRV (приоритет, вес, порт и целевое значение) указываются отдельно для каждой записи в наборе записей.

Примеры имен записей SRV (имя службы — SIP, протокол — TCP):

- \_sip.\_tcp (создает набор записей на вершине зоны);
- \_sip.\_tcp.sipservice (создает набор записей с именем sipservice).

### <a name="recommended-articles"></a>Recommended articles

* [Зоны и записи DNS](dns-zones-records.md)
* [Get started with Azure DNS using the Azure portal](dns-getstarted-create-recordset-portal.md) (Начало работы с Azure DNS с использованием портала Azure)
* [SRV-запись (Википедия)](https://en.wikipedia.org/wiki/SRV_record)


## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со статьей [Зоны и записи DNS](dns-zones-records.md).
* Чтобы начать работу с Azure DNS, узнайте, как [создать зону DNS](dns-getstarted-create-dnszone-portal.md) и [записи DNS](dns-getstarted-create-recordset-portal.md).
* Чтобы перенести имеющуюся зону DNS, узнайте, как [импортировать и экспортировать файл зоны DNS](dns-import-export.md).

