---
title: Журналы IIS в службе Log Analytics | Документация Майкрософт
description: Службы IIS (Internet Information Services) хранят данные об активности пользователей в файлах журналов, собираемых службой Log Analytics.  В этой статье описано, как настроить сбор журналов IIS и сведения о записях, созданных ими в службе Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: cd63c63344f322f7d761a2907f52e97f1009e3b8
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54101961"
---
# <a name="collect-iis-logs-in-log-analytics"></a>Сбор журналов IIS в Log Analytics
Службы IIS (Internet Information Services) хранят данные об активности пользователей в файлах журналов, собираемых службой Log Analytics и сохраняемых как [данные журнала](data-collection.md).

![Журналы IIS](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Настройка журналов IIS
Служба Log Analytics собирает записи из файлов журналов, созданных службами IIS, поэтому вам необходимо [настроить IIS для ведения журнала](https://technet.microsoft.com/library/hh831775.aspx).

Служба Log Analytics поддерживает только те файлы журналов IIS, которые хранятся в формате W3C, и не поддерживает настраиваемые поля или расширенное ведение журналов IIS. Она не собирает журналы в формате NCSA или в собственном формате IIS.

Журналы IIS настраиваются в Log Analytics в меню [Дополнительные параметры](agent-data-sources.md#configuring-data-sources).  Никакие настройки, кроме выбора параметра **Сбор файлов журналов IIS в формате W3C**, не требуются.


## <a name="data-collection"></a>Сбор данных
Служба Log Analytics собирает записи журналов IIS от каждого агента каждый раз, когда журнал закрывается и создается новый. Эта частота определяется с помощью параметра **Log File Rollover Schedule** (Расписание переключения на файл продолжения журнала) для сайта IIS. Значение по умолчанию — один раз в день. Например, если для этого параметра задано значение **Ежечасно**, служба Log Analytics будет собирать данные журнала каждый час.  Если задано значение **Ежедневно**, служба Log Analytics будет собирать данные журнала каждые 24 часа.


## <a name="iis-log-record-properties"></a>Свойства записей в журналах IIS
Записи в журналах IIS относятся к типу **W3CIISLog** и обладают свойствами, описанными в таблице ниже.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| Компьютер |Имя компьютера, с которого было получено событие. |
| cIP |IP-адрес клиента. |
| csMethod |Метод запроса, такой как GET или POST. |
| csReferer |Сайт, с которого пользователь перешел на текущий сайт. |
| csUserAgent |Тип браузера клиента. |
| csUserName |Имя прошедшего проверку пользователя, который подключился к серверу. Анонимные пользователи обозначаются дефисом. |
| csUriStem |Целевой объект запроса, такой как веб-страница. |
| csUriQuery |Запрос, который пытался выполнить клиент (если есть). |
| ManagementGroupName |Имя группы управления для агентов Operations Manager.  Для других агентов это AOI-\<идентификатор_рабочей_области\>. |
| RemoteIPCountry |Страна IP-адреса клиента. |
| RemoteIPLatitude |Широта IP-адреса клиента. |
| RemoteIPLongitude |Долгота IP-адреса клиента. |
| scStatus |Код состояния HTTP. |
| scSubStatus |Код ошибки подсостояния . |
| scWin32Status |Код состояния Windows. |
| sIP |IP-адрес веб-сервера. |
| SourceSystem |OpsMgr |
| sPort |Порт на сервере, к которому подключен клиент. |
| sSiteName |Имя сайта IIS. |
| TimeGenerated |Дата и время регистрации записи. |
| TimeTaken |Время обработки запроса в миллисекундах. |

## <a name="log-queries-with-iis-logs"></a>Запросы для получения записей журналов IIS
В следующей таблице представлены различные примеры запросов к журналу, извлекающих записи из журналов IIS.

| Запрос | ОПИСАНИЕ |
|:--- |:--- |
| W3CIISLog |Все записи в журнале IIS. |
| W3CIISLog &#124; where scStatus==500 |Все записи журнала IIS с состоянием возврата 500. |
| W3CIISLog &#124; summarize count() by cIP |Число записей в журнале IIS по IP-адресу клиента. |
| W3CIISLog &#124; where csHost=="www.contoso.com" &#124; summarize count() by csUriStem |Число записей в журнале IIS по URL-адресу для узла www.contoso.com. |
| W3CIISLog &#124; summarize sum(csBytes) by Computer &#124; take 500000 |Общее количество байтов, полученных каждым компьютером IIS. |

## <a name="next-steps"></a>Дополнительная информация
* Настройте службу Log Analytics для сбора других [источников данных](agent-data-sources.md) для анализа.
* Узнайте больше о [запросах журнала](../../log-analytics/log-analytics-queries.md), которые можно применять для анализа данных, собираемых из источников данных и решений.
