---
title: Основные отличия для Службы машинного обучения (Предварительная версия)
description: В этой статье описываются основные различия между Службы машинного обучения в Управляемый экземпляр SQL Azure и SQL Server Службы машинного обучения.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 10/26/2020
ms.openlocfilehash: c806c0a13f9f5f13588b780054d1f285beb44802
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324539"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Основные различия между Службами машинного обучения в Управляемом экземпляре SQL Azure и SQL Server

Функции [службы машинного обучения в Azure SQL управляемый экземпляр (Предварительная версия)](machine-learning-services-overview.md) практически идентичны [SQL Server службы машинного обучения](/sql/advanced-analytics/what-is-sql-server-machine-learning). Ниже приведены некоторые ключевые отличия.

> [!IMPORTANT]
> Службы машинного обучения в Azure SQL Управляемый экземпляр в настоящее время находится в общедоступной предварительной версии. Чтобы зарегистрироваться, см. статью [Регистрация для получения предварительной версии](machine-learning-services-overview.md#signup).

## <a name="preview-limitations"></a>Ограничения предварительной версии

На этапе предварительной версии служба имеет следующие ограничения:

- Замыкание соединений не работает (см. сведения о [замыкании соединения на SQL Server из скрипта Python или R](/sql/machine-learning/connect/loopback-connection)).
- Внешние пулы ресурсов не поддерживаются.
- Поддерживаются только Python и R. Внешние языки, такие как Java, добавить нельзя.
- Сценарии, использующие [интерфейс передачи сообщений](/message-passing-interface/microsoft-mpi) (MPI), не поддерживаются.

В случае обновления цели уровня обслуживания обновите уровень обслуживания и создайте запрос в службу поддержки, чтобы повторно включить выделенные ограничения ресурсов для R/Python.

## <a name="language-support"></a>Поддержка языков

Службы машинного обучения в SQL Управляемый экземпляр и SQL Server поддерживают как среду расширения Python, так и [платформу расширяемости](/sql/advanced-analytics/concepts/extensibility-framework)R. Основные отличия указаны далее.

- Начальные версии Python и R отличаются в Службы машинного обучения SQL Управляемый экземпляр и SQL Server:

  | Система               | Python | R     |
  |----------------------|--------|-------|
  | Управляемый экземпляр SQL | 3.7.1  | 3.5.2 |
  | SQL Server           | 3.5.2  | 3.3.3 |

- Нет необходимости настраивать `external scripts enabled` через `sp_configure`. После [регистрации](machine-learning-services-overview.md#signup) в предварительной версии машинное обучение включено для управляемый экземпляр Azure SQL.

## <a name="packages"></a>Пакеты

Управление пакетами Python и R работает по-разному между SQL Управляемый экземпляр и SQL Server. в частности:

- Пакеты, которые зависят от внешних сред выполнения (например, Java) или которым требуется доступ к API операционной системы для установки или использования, не поддерживаются.
- Пакеты могут выполнять исходящие сетевые вызовы (от предыдущего в предварительной версии). Вы можете задать правильные правила безопасности для исходящего трафика на уровне [группы безопасности сети](../../virtual-network/network-security-groups-overview.md) , чтобы включить исходящие сетевые вызовы.

Дополнительные сведения об управлении пакетами Python и R см. в следующих статьях:

- [Получение сведений о пакете Python](/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [Получение сведений о пакете R](/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>Управление ресурсами

Невозможно ограничить ресурсы R с помощью [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) и внешних пулов ресурсов.

В общедоступной предварительной версии для ресурсов R задано ограничение в 20 % от ресурсов Управляемого экземпляра SQL, которое также зависит от выбранного уровня обслуживания. Дополнительные сведения см. в статье [Модели приобретения Базы данных SQL Azure](../database/purchasing-models.md).

### <a name="insufficient-memory-error"></a>Ошибка "недостаточно памяти"

Если для выполнения кода R недостаточно памяти, отобразится сообщение об ошибке. Распространенные сообщения об ошибках:

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

Использование памяти зависит от объема памяти, используемого скриптами R, и от количества выполняемых параллельных запросов. Если у вас возникают приведенные выше ошибки, вы можете перенести базу данных на более высокий уровень обслуживания, чтобы решить эту проблему.

## <a name="next-steps"></a>Дальнейшие действия

- См. Обзор [службы машинного обучения в управляемый экземпляр SQL Azure](machine-learning-services-overview.md).
- Сведения об использовании Python в Службы машинного обучения см. в разделе [выполнение скриптов Python](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Сведения об использовании R в Службы машинного обучения см. в разделе [выполнение скриптов r](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).