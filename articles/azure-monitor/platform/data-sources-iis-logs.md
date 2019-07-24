---
title: Журналы IIS в Azure Monitor | Документация Майкрософт
description: Службы IIS (Internet Information Services) хранят данные об активности пользователей в файлах журналов, собираемых службой Azure Monitor.  В этой статье описано, как настроить сбор журналов IIS и сведения о записях, созданных ими в службе Azure Monitor.
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
ms.openlocfilehash: 05d9dc8f676589dcb301c19b0a2e80e9fd4c1fa0
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249745"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>Сбор журналов IIS в Azure Monitor
Службы IIS (Internet Information Services) хранят данные об активности пользователей в файлах журналов, собираемых службой Azure Monitor и сохраняемых как [данные журнала](data-platform.md).

![Журналы IIS](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Настройка журналов IIS
Служба Azure Monitor собирает записи из файлов журналов, созданных службами IIS, поэтому вам необходимо [настроить IIS для ведения журнала](https://technet.microsoft.com/library/hh831775.aspx).

Служба Azure Monitor поддерживает только те файлы журналов IIS, которые хранятся в формате W3C, и не поддерживает настраиваемые поля или расширенное ведение журналов IIS. Она не собирает журналы в формате NCSA или в собственном формате IIS.

Журналы IIS настраиваются в Azure Monitor в меню [Дополнительные параметры](agent-data-sources.md#configuring-data-sources).  Никакие настройки, кроме выбора параметра **Сбор файлов журналов IIS в формате W3C**, не требуются.


## <a name="data-collection"></a>Сбор данных
Azure Monitor собирает записи журнала IIS из каждого агента каждый раз, когда изменяется отметка времени журнала или создается новый файл. Журнал считывается каждые 5 минут. Частота создания нового файла определяется параметром **расписания развертывания файла журнала** для сайта IIS, который по умолчанию имеет значение один раз в день. Если по какой-либо причине IIS не обновляет метку времени до времени переключения, если параметр имеет значение ежечасно, Azure Monitor собирает журнал каждый час. Если параметр имеет значение **ежедневно**, Azure Monitor собирает журнал каждые 24 часа.


## <a name="iis-log-record-properties"></a>Свойства записей в журналах IIS
Записи в журналах IIS относятся к типу **W3CIISLog** и обладают свойствами, описанными в таблице ниже.

| Свойство | Описание |
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
| RemoteIPCountry |Страна или регион IP-адреса клиента. |
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

| Запрос | Описание |
|:--- |:--- |
| W3CIISLog |Все записи в журнале IIS. |
| W3CIISLog &#124; where scStatus==500 |Все записи журнала IIS с состоянием возврата 500. |
| W3CIISLog &#124; summarize count() by cIP |Число записей в журнале IIS по IP-адресу клиента. |
| W3CIISLog &#124; , где кшост = = "\.www contoso.com &#124; " резюмируing Count () by ксуристем |Число записей журнала IIS по URL-адресу узла www\.contoso.com. |
| W3CIISLog &#124; summarize sum(csBytes) by Computer &#124; take 500000 |Общее количество байтов, полученных каждым компьютером IIS. |

## <a name="next-steps"></a>Следующие шаги
* Настройте в службе Azure Monitor сбор других [источников данных](agent-data-sources.md) для анализа.
* Узнайте больше о [запросах журнала](../log-query/log-query-overview.md), которые можно применять для анализа данных, собираемых из источников данных и решений.
