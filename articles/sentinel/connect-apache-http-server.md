---
title: Подключение HTTP-сервера Apache к Azure Sentinel | Документация Майкрософт
description: Узнайте, как использовать соединитель Apache HTTP Server для извлечения журналов Apache в Azure Sentinel. Просмотр данных Apache в книгах, создание оповещений и улучшение расследования.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 59ef34f9d6d42277d8e83bf4e0ebb8ee29096f52
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566870"
---
# <a name="connect-your-apache-http-server-to-azure-sentinel"></a>Подключение HTTP-сервера Apache к Azure Sentinel

> [!IMPORTANT]
> Соединитель HTTP-сервера Apache в настоящее время находится на **этапе предварительной версии**. Ознакомьтесь с дополнительными [условиями использования Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) предварительных версий для дополнительных юридических условий, которые относятся к функциям Azure, которые доступны в бета-версии, предварительном просмотре или еще не выпущены в общедоступную версию.

В этой статье объясняется, как подключить HTTP-сервер Apache к Azure Sentinel. Соединитель HTTP-сервера Apache позволяет легко получать журналы HTTP-сервера Apache в Azure Sentinel, чтобы можно было просматривать данные в книгах, запрашивать их для создания пользовательских оповещений и внедрять их для улучшения расследования. Интеграция между сервером Apache HTTP и Azure Sentinel использует локальную обработку файлов агентом Log Analytics.

> [!NOTE]
> Данные будут храниться в географическом расположении рабочей области, на которой вы используете метку Azure.

## <a name="prerequisites"></a>Предварительные требования

- У вас должно быть разрешение на запись в рабочей области "Sentinel" Azure.

## <a name="configure-and-integrate-apache-http-server-logs-via-log-analytics-agent"></a>Настройка и интеграция журналов HTTP-сервера Apache с помощью агента Log Analytics

Настройте расположение файлов журнала HTTP-сервера Apache, чтобы перенаправить данные в рабочую область Azure с помощью агента Log Analytics.
Настройка агента Log Analytics для чтения файлов журнала HTTP-сервера Apache.

1. Следуйте инструкциям по https://httpd.apache.org/docs/2.4/logs.html настройке расположения файлов журнала на сервере Apache HTTP.

1. В меню навигации меток Azure выберите **соединители данных** , а затем выберите **http-сервер Apache (Предварительная версия)**.

1. Выберите **открыть страницу соединителя**.

1. Следуйте инструкциям на странице HTTP-сервера Apache.

## <a name="find-your-data"></a>Поиск данных

После установки успешного подключения данные отображаются в Log Analytics в разделе ApacheHTTPServer_CL.

## <a name="validate-connectivity"></a>Проверка подключения

После того, как журналы начнут отображаться в Log Analytics, может пройти до 20 минут.

## <a name="next-steps"></a>Следующие шаги

В этом документе вы узнали, как подключить HTTP-сервер Apache к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.

- Узнайте, как [получить представление о данных и потенциальных угрозах](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Используйте книги](tutorial-monitor-your-data.md) для мониторинга данных.
