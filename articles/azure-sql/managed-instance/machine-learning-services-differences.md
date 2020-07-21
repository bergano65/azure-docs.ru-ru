---
title: Основные отличия для Службы машинного обучения (Предварительная версия)
description: В этом разделе описываются основные различия между Службы машинного обучения в Управляемый экземпляр SQL Azure и SQL Server Службы машинного обучения.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: carlrab, davidph
manager: cgronlun
ms.date: 05/27/2020
ms.openlocfilehash: f267b155fe21e5dee1a7c488c999fe194ed38eb1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86504124"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Основные различия между Службами машинного обучения в Управляемом экземпляре SQL Azure и SQL Server

Функции [службы машинного обучения в Azure SQL управляемый экземпляр (Предварительная версия)](machine-learning-services-overview.md) практически идентичны [SQL Server службы машинного обучения](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Ниже приведены некоторые ключевые отличия.

> [!IMPORTANT]
> Службы машинного обучения в Azure SQL Управляемый экземпляр в настоящее время находится в общедоступной предварительной версии. Чтобы зарегистрироваться, см. статью [Регистрация для получения предварительной версии](machine-learning-services-overview.md#signup).

## <a name="preview-limitations"></a>Ограничения предварительной версии

На этапе предварительной версии служба имеет следующие ограничения:

- Замыкание соединений не работает (см. сведения о [замыкании соединения на SQL Server из скрипта Python или R](/sql/machine-learning/connect/loopback-connection)).
- Внешние пулы ресурсов не поддерживаются.
- Поддерживаются только Python и R. Внешние языки, такие как Java, добавить нельзя.
- Сценарии, использующие [интерфейс передачи сообщений](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) (MPI), не поддерживаются.

Если вы решите обновить целевой уровень обслуживания, измените его и отправьте запрос в службу поддержки, чтобы восстановить ограничения выделенных ресурсов для R/Python.

## <a name="language-support"></a>Поддержка языков

Службы машинного обучения в SQL Управляемый экземпляр и SQL Server поддерживают как среду расширения Python, так и [платформу расширяемости](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)R. Ниже описаны основные различия.

- Начальные версии Python и R отличаются в Службы машинного обучения SQL Управляемый экземпляр и SQL Server:

  | Система               | Python | R     |
  |----------------------|--------|-------|
  | Управляемый экземпляр SQL | 3.7.1  | 3.5.2 |
  | SQL Server           | 3.5.2  | 3.3.3 |

- Нет необходимости настраивать `external scripts enabled` через `sp_configure`. После [регистрации](machine-learning-services-overview.md#signup) в предварительной версии машинное обучение включено для управляемый экземпляр Azure SQL.

## <a name="packages"></a>Пакеты

Управление пакетами Python и R работает по-разному между SQL Управляемый экземпляр и SQL Server. в частности:

- Пакеты не могут выполнять исходящие сетевые вызовы. Это ограничение аналогично [правилам брандмауэра по умолчанию для службы машинного обучения](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) в SQL Server, но не может быть изменено в управляемый экземпляр SQL.
- Пакеты, которые зависят от внешних сред выполнения (например, Java) или которым требуется доступ к API операционной системы для установки или использования, не поддерживаются.

Дополнительные сведения об управлении пакетами Python и R см. в следующих статьях:

- [Получение сведений о пакете Python](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)
- [Получение сведений о пакете R](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)

## <a name="resource-governance"></a>Управление ресурсами

Невозможно ограничить ресурсы R с помощью [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) и внешних пулов ресурсов.

В общедоступной предварительной версии для ресурсов R задано ограничение в 20 % от ресурсов Управляемого экземпляра SQL, которое также зависит от выбранного уровня обслуживания. Дополнительные сведения см. в статье [Модели приобретения Базы данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Ошибка "недостаточно памяти"

Если для выполнения кода R недостаточно памяти, отобразится сообщение об ошибке. Распространенные сообщения об ошибках:

- Не удается связаться со средой выполнения для скрипта "R" для идентификатора запроса: * * * * * * *. Проверьте требования среды выполнения R.
- Во время выполнения sp_execute_external_script произошла ошибка в скрипте R с HRESULT 0x80004004. Возникла ошибка во внешнем скрипте: ..could not allocate memory (0 Mb) in C function R_AllocStringBuffer (не удалось выделить память (0 МБ) в функции C R_AllocStringBuffer).
- Произошла ошибка внешнего скрипта: ошибка: не удается выделить вектор размера.

Использование памяти зависит от объема памяти, используемого скриптами R, и от количества выполняемых параллельных запросов. Если у вас возникают приведенные выше ошибки, вы можете перенести базу данных на более высокий уровень обслуживания, чтобы решить эту проблему.

## <a name="next-steps"></a>Дальнейшие действия

- См. Обзор [службы машинного обучения в управляемый экземпляр SQL Azure](machine-learning-services-overview.md).
- Сведения об использовании Python в Службы машинного обучения см. в разделе [выполнение скриптов Python](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
- Сведения об использовании R в Службы машинного обучения см. в разделе [выполнение скриптов r](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
