---
title: Установка Visual Studio и SSDT для хранилища данных SQL | Документация Майкрософт
description: Установка Visual Studio и SQL Server Data Tools (SSDT) для хранилища данных SQL Azure.
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/05/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: aa904ac62ce41cc89945ddfdef2e426143e42bb4
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479496"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Установка Visual Studio и SSDT для хранилища данных SQL
Используйте Visual Studio 2019 для разработки приложений для хранилища данных SQL. Сейчас Visual Studio 2019 SSDT не поддерживается для хранилища данных SQL. 

Использование Visual Studio с SSDT позволяет использовать обозреватель объектов SQL Server для изучения таблиц, представлений, хранимых процедур и многих других объектов в хранилище данных SQL. Он также позволяет выполнять запросы.

> [!NOTE]
> Хранилище данных SQL пока не поддерживает проекты базы данных Visual Studio. Для получения периодических обновлений об этой функции проголосуйте на сайте [UserVoice].
> 
> 

## <a name="step-1-install-visual-studio"></a>Шаг 1.: Установка Visual Studio
Скачайте и установите Visual Studio, используя приведенные ниже ссылки. Если приложение Visual Studio 2013 или его более поздняя версия уже установлена, можно перейти к шагу 2 и установить SSDT.

1. [Скачайте Visual Studio][].
2. Выполните установку, следуя инструкциям по [установке Visual Studio][Installing Visual Studio] с сайта MSDN, а затем выберите настройки по умолчанию.

## <a name="step-2-install-ssdt"></a>Шаг 2.: Установить SSDT
Чтобы установить SSDT для Visual Studio, сначала проверьте наличие обновления SSDT в Visual Studio, выполнив следующие действия.

1. В Visual Studio щелкните **Сервис** / **Расширения и обновления…** / **Обновления**
2. Выберите **Обновления продукта** и найдите элемент **Обновление Microsoft SQL Server для средств работы с базами данных**.

Если обновление не найдено, должна быть установлена последняя версия. Чтобы убедиться, что компонент SSDT установлен, выберите **Справка** / **О Microsoft Visual Studio** и найдите в списке SQL Server Data Tools. Если параметр для установки недоступен в Visual Studio, можно посетить страницу [загрузки SSDT][SSDT Download] , чтобы скачать и установить SSDT вручную.

## <a name="next-steps"></a>Следующие шаги
Теперь, когда у вас установлена последняя версия SSDT, вы можете [Подключиться][connect] к хранилищу данных SQL.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Скачайте Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
