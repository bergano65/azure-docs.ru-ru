---
title: Установка Visual Studio и SSDT для хранилища данных SQL | Документация Майкрософт
description: Установка Visual Studio и SQL Server Data Tools (SSDT) для хранилища данных SQL Azure.
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/05/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: b2e34f1f72b1b0aa76d4a3031102d052118dae5f
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304130"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Установка Visual Studio и SSDT для хранилища данных SQL
Используйте 2019 г. Visual Studio для разработки приложений для хранилища данных SQL. В настоящее время Visual Studio 2019 SSDT не поддерживается для хранилища данных SQL. 

С помощью Visual Studio с SSDT можно использовать обозреватель объектов SQL Server для анализа таблиц, представлений, хранимых процедур и многих других объектов визуально в хранилище данных SQL. Он также позволяет выполнять запросы.

> [!NOTE]
> Хранилище данных SQL пока не поддерживает проекты базы данных Visual Studio. Для получения периодических обновлений об этой функции проголосуйте на сайте [UserVoice].
> 
> 

## <a name="step-1-install-visual-studio"></a>Шаг 1. Установка Visual Studio
Скачайте и установите Visual Studio, используя приведенные ниже ссылки. Если приложение Visual Studio 2013 или его более поздняя версия уже установлена, можно перейти к шагу 2 и установить SSDT.

1. [Скачайте Visual Studio][].
2. Выполните установку, следуя инструкциям по [установке Visual Studio][Installing Visual Studio] с сайта MSDN, а затем выберите настройки по умолчанию.

## <a name="step-2-install-ssdt"></a>Шаг 2. Установить SSDT
Чтобы установить SSDT для Visual Studio, сначала проверьте наличие обновления SSDT в Visual Studio, выполнив следующие действия.

1. В Visual Studio щелкните **Сервис** / **Расширения и обновления…** / **Обновления**
2. Выберите **Обновления продукта** и найдите элемент **Обновление Microsoft SQL Server для средств работы с базами данных**.

Вы должны иметь установлена, если обновление не найдена последняя версия. Чтобы убедиться, что компонент SSDT установлен, выберите **Справка** / **О Microsoft Visual Studio** и найдите в списке SQL Server Data Tools. Если выбрать установку недоступен из Visual Studio, вы можете посетить [загрузки SSDT] [ SSDT Download] страницу, чтобы скачать и установить SSDT вручную.

## <a name="next-steps"></a>Дальнейшие действия
Теперь у вас последняя версия SSDT, вы будете готовы [подключения] [ connect] в хранилище данных SQL.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Скачайте Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
