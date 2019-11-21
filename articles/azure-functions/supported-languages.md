---
title: Поддерживаемые языки в решении "Функции Azure"
description: Узнайте, какие языки поддерживаются (являются общедоступными), а какие используются в режиме экспериментальной или предварительной версии.
ms.topic: conceptual
ms.date: 08/02/2018
ms.openlocfilehash: 3b61dd83e481d42dd15f089247d016c6b71ff0de
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230286"
---
# <a name="supported-languages-in-azure-functions"></a>Поддерживаемые языки в решении "Функции Azure"

В этой статье перечислены уровни поддержки языков, используемых с решением "Функции Azure".

## <a name="levels-of-support"></a>Уровни поддержки

Уровней поддержки три:

* **Общедоступные** — полностью поддерживаемые языки, утвержденные для использования в рабочей среде.
* **Предварительная версия** — языки, которые еще не поддерживаются, но в будущем станут общедоступными.
* **Экспериментальные** — языки, которые не поддерживаются и могут быть удалены в будущем. Нет гарантии, что они будут поддерживаться в качестве общедоступных.

## <a name="languages-by-runtime-version"></a>Languages by runtime version 

Доступны [две версии среды выполнения в решении "Функции Azure"](functions-versions.md). В следующей таблице показаны поддерживаемые языки для каждой версии среды выполнения.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Языки, используемые в режиме экспериментальной версии

Языки, используемые как экспериментальные, в версии 1.x не масштабируются должным образом и поддерживают не все привязки.

Но не используйте экспериментальные функции для важных задач, так как эти функции не поддерживаются официально. Случаи поддержки не следует открывать для проблем, если применяются языки, используемые в качестве экспериментальных. 

В среде выполнения версии 2.x не предоставляется поддержка языков, используемых в качестве экспериментальных. Поддержка новых языков добавляется только в том случае, если язык может поддерживаться в рабочей среде. 

### <a name="language-extensibility"></a>Расширяемость языка

Starting with version 2.x, the runtime is designed to offer [language extensibility](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Языки JavaScript и Java в среде выполнения 2.x поддерживают эту расширяемость.

## <a name="next-steps"></a>Дальнейшие действия

To learn more about how to develop functions in the supported languages, see the following resources:

+ [C# class library developer reference](functions-dotnet-class-library.md)
+ [C# script developer reference](functions-reference-csharp.md)
+ [Java developer reference](functions-reference-java.md)
+ [JavaScript developer reference](functions-reference-node.md)
+ [PowerShell developer reference](functions-reference-powershell.md)
+ [Python developer reference](functions-reference-python.md)
+ [TypeScript developer reference](functions-reference-node.md#typescript)
