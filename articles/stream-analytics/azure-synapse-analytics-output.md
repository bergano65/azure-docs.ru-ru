---
title: Выходные данные аналитики Azure синапсе из Azure Stream Analytics
description: В этой статье описывается Azure синапсе Analytics в качестве выходных данных для Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 0b3bec9c4d4476b95279e35953ff89177f4488d4
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305843"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics"></a>Выходные данные аналитики Azure синапсе из Azure Stream Analytics

[Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) (ранее — Хранилище данных SQL) — это служба аналитики без ограничений, которая объединяет корпоративные хранилища данных и аналитику больших данных. 

Azure Stream Analytics задания могут выводить данные в выделенную таблицу пула SQL в Azure синапсе Analytics, а также обрабатывать пропускную способность до 200 МБ/с. Это поддерживает наиболее ресурсоемкие аналитики в реальном времени и обработку данных с возможностью оперативного пути для рабочих нагрузок, таких как отчеты и панели мониторинга.  

Выделенная таблица пула SQL должна существовать, прежде чем ее можно будет добавить в качестве выходных данных в Stream Analytics задание. Схема таблицы должна соответствовать полям и их типам в выходных данных задания. 

Чтобы использовать Azure Synapse в качестве выходных данных, необходимо убедиться в том, что учетная запись хранения настроена. Перейдите к параметрам учетной записи хранения, чтобы настроить ее. Разрешены только типы учетных записей хранения, поддерживающие таблицы: Общего назначения версии 2 и общего назначения версии 1. Выберите только категорию "Стандартный". Категория "Премиум" не поддерживается.

## <a name="output-configuration"></a>Конфигурация выходных данных

В таблице ниже перечислены имена свойств и даны их описания для создания выходных данных Azure Synapse Analytics.

|Имя свойства|Описание|
|-|-|
|Псевдоним выходных данных |Понятное имя, которое используется в запросах для направления выходных данных запроса в соответствующую базу данных. |
|База данных |имя выделенного пула SQL, куда отправляются выходные данные. |
|Имя сервера |Имя сервера Azure Synapse.  |
|Имя пользователя |Имя пользователя, имеющего доступ к базе данных на запись. Stream Analytics поддерживает только проверку подлинности SQL. |
|Пароль |Пароль для подключения к базе данных. |
|Таблица  | Имя таблицы, в которую записываются выходные данные. В имени таблицы учитывается регистр. Схема этой таблицы должна точно соответствовать количеству полей и их типов, формируемых выходными данными задания.|

## <a name="next-steps"></a>Дальнейшие действия

* [Краткое руководство. по созданию задания Stream Analytics с помощью портала Azure](stream-analytics-quick-create-portal.md)
* [Краткое руководство. Создание задания Azure Stream Analytics с помощью Azure CLI](quick-create-azure-cli.md)
* [Краткое руководство. Создание задания Azure Stream Analytics с помощью шаблона ARM](quick-create-azure-resource-manager.md)
* [Краткое руководство. Создание задания Stream Analytics с помощью Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Краткое руководство. Создание задания Azure Stream Analytics с помощью Visual Studio](stream-analytics-quick-create-vs.md)
* [Краткое руководство. Создание задания Azure Stream Analytics в Visual Studio Code](quick-create-visual-studio-code.md)