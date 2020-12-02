---
title: Начало работы с функциями Azure
description: Выполните первые шаги для работы с функциями Azure.
author: craigshoemaker
ms.topic: overview
ms.date: 11/19/2020
ms.author: cshoe
zone_pivot_groups: programming-languages-set-functions-lang-workers
ms.openlocfilehash: 6aba58dad9853714bf26442592f74fc77e39765c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94975016"
---
# <a name="getting-started-with-azure-functions"></a>Начало работы с функциями Azure

## <a name="introduction"></a>Введение

[Функции Azure](./functions-overview.md) позволяют реализовать логику системы в готовых блоках кода. Эти блоки кода называются "функциями".

Воспользуйтесь следующими ресурсами, чтобы начать работу.

::: zone pivot="programming-language-csharp"

| Действие | Ресурсы |
| --- | --- |
| **Создание первой функции** | С помощью одного из следующих средств<br><br><li>[Visual Studio Code](./functions-create-your-first-function-visual-studio.md)<li>[Visual Studio](./create-first-function-vs-code-csharp.md)<li>[Командная строка](./create-first-function-cli-csharp.md) |
| **можно увидеть функцию, выполняющую** | <li>[Браузер Azure Samples](https://docs.microsoft.com/samples/browse/?languages=csharp&expanded=azure&products=azure-functions)<li>[Azure Community Library](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=C%23) |
| **Знакомство с интерактивным учебником**| <li>[Выбор оптимальной бессерверной технологии Azure для бизнес-сценария](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Хорошо спроектированная платформа. Эффективность](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Выполнение функции Azure с помощью триггеров](https://docs.microsoft.com/learn/modules/execute-azure-function-with-triggers/) <br><br>[Полный список интерактивных учебников](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions) см. в Microsoft Learn.|
| **Узнать больше** | <li>Узнайте, как функции [автоматически увеличивают или уменьшают](./functions-scale.md) количество экземпляров в соответствии с потребностями.<li>Изучите различные доступные [методы развертывания](./functions-deployment-technologies.md)<li>Используйте встроенные [средства мониторинг](./functions-monitoring.md) для анализа функций<li>Ознакомьтесь со [Справочником по C#](./functions-dotnet-class-library.md)|

::: zone-end

::: zone pivot="programming-language-java"
| Действие | Ресурсы |
| --- | --- |
| **Создание первой функции** | С помощью одного из следующих средств<br><br><li>[Visual Studio Code](./create-first-function-vs-code-java.md)<li>[Функция Java/Maven в терминале / командной строке](./create-first-function-cli-java.md)<li>[Gradle](./functions-create-first-java-gradle.md)<li>[Eclipse](./functions-create-maven-eclipse.md)<li>[IntelliJ IDEA](./functions-create-maven-intellij.md) |
| **можно увидеть функцию, выполняющую** | <li>[Браузер Azure Samples](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=java)<li>[Azure Community Library](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Java) |
| **Знакомство с интерактивным учебником**| <li>[Выбор оптимальной бессерверной технологии Azure для бизнес-сценария](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Хорошо спроектированная платформа. Эффективность](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Разработка приложения с помощью подключаемого модуля Maven для Функций Azure](https://docs.microsoft.com/learn/modules/develop-azure-functions-app-with-maven-plugin/) <br><br>[Полный список интерактивных учебников](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions) см. в Microsoft Learn.|
| **Узнать больше** | <li>Узнайте, как функции [автоматически увеличивают или уменьшают](./functions-scale.md) количество экземпляров в соответствии с потребностями.<li>Изучите различные доступные [методы развертывания](./functions-deployment-technologies.md)<li>Используйте встроенные [средства мониторинг](./functions-monitoring.md) для анализа функций<li>Ознакомьтесь со [Справочником по Java](./functions-reference-java.md)|
::: zone-end

::: zone pivot="programming-language-javascript"
| Действие | Ресурсы |
| --- | --- |
| **Создание первой функции** | С помощью одного из следующих средств<br><br><li>[Visual Studio Code](./create-first-function-vs-code-node.md)<li>[Функция Node.js в терминале / командной строке](./create-first-function-cli-java.md) |
| **можно увидеть функцию, выполняющую** | <li>[Браузер Azure Samples](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=javascript%2Ctypescript)<li>[Azure Community Library](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=JavaScript%2CTypeScript) |
| **Знакомство с интерактивным учебником** | <li>[Выбор оптимальной бессерверной технологии Azure для бизнес-сценария](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Хорошо спроектированная платформа. Эффективность](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Создание бессерверных API с помощью Функций Azure](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Создание бессерверной логики с помощью Функций Azure](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Рефакторинг API-интерфейсов Node.js и Express в бессерверные API с помощью Функций Azure](https://docs.microsoft.com/learn/modules/shift-nodejs-express-apis-serverless/) <br><br>[Полный список интерактивных учебников](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions) см. в Microsoft Learn.|
| **Узнать больше** | <li>Узнайте, как функции [автоматически увеличивают или уменьшают](./functions-scale.md) количество экземпляров в соответствии с потребностями.<li>Изучите различные доступные [методы развертывания](./functions-deployment-technologies.md)<li>Используйте встроенные [средства мониторинг](./functions-monitoring.md) для анализа функций<li>Ознакомьтесь со справочником по [JavaScript](./functions-reference-node.md) или [TypeScript](./functions-reference-node.md#typescript)|
::: zone-end

::: zone pivot="programming-language-powershell"
| Действие | Ресурсы |
| --- | --- |
| **Создание первой функции** | <li>Использование [Visual Studio Code](./create-first-function-vs-code-powershell.md) |
| **можно увидеть функцию, выполняющую** | <li>[Браузер Azure Samples](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=powershell)<li>[Azure Community Library](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=PowerShell) |
| **Знакомство с интерактивным учебником** | <li>[Выбор оптимальной бессерверной технологии Azure для бизнес-сценария](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Хорошо спроектированная платформа. Эффективность](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Создание бессерверных API с помощью Функций Azure](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Создание бессерверной логики с помощью Функций Azure](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Выполнение функции Azure с помощью триггеров](https://docs.microsoft.com/learn/modules/execute-azure-function-with-triggers/) <br><br>[Полный список интерактивных учебников](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions) см. в Microsoft Learn.|
| **Узнать больше** | <li>Узнайте, как функции [автоматически увеличивают или уменьшают](./functions-scale.md) количество экземпляров в соответствии с потребностями.<li>Изучите различные доступные [методы развертывания](./functions-deployment-technologies.md)<li>Используйте встроенные [средства мониторинг](./functions-monitoring.md) для анализа функций<li>Ознакомьтесь со [справочником по PowerShell](./functions-reference-powershell.md))|
::: zone-end

::: zone pivot="programming-language-python"
| Действие | Ресурсы |
| --- | --- |
| **Создание первой функции** | С помощью одного из следующих средств<br><br><li>[Visual Studio Code](./functions-create-first-function-vs-code.md?pivots=programming-language-python)<li>[Терминал / командная строка](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-python) |
| **можно увидеть функцию, выполняющую** | <li>[Браузер Azure Samples](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=python)<li>[Azure Community Library](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Python) |
| **Знакомство с интерактивным учебником** | <li>[Выбор оптимальной бессерверной технологии Azure для бизнес-сценария](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Хорошо спроектированная платформа. Эффективность](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Создание бессерверных API с помощью Функций Azure](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Создание бессерверной логики с помощью Функций Azure](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/) <br><br>[Полный список интерактивных учебников](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions) см. в Microsoft Learn.|
| **Узнать больше** | <li>Узнайте, как функции [автоматически увеличивают или уменьшают](./functions-scale.md) количество экземпляров в соответствии с потребностями.<li>Изучите различные доступные [методы развертывания](./functions-deployment-technologies.md)<li>Используйте встроенные [средства мониторинг](./functions-monitoring.md) для анализа функций<li>Ознакомьтесь со [Справочником по Python](./functions-reference-python.md)|
::: zone-end

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Анатомия приложения Функций Azure](./functions-reference.md)
