---
title: Краткое руководство. Создание и использование цикла обучения с помощью пакета SDK — Персонализатор
description: В этом кратком руководстве показано, как создать базу знаний и управлять ею с использованием клиентского пакета SDK.
ms.topic: quickstart
ms.date: 04/27/2020
ms.custom: tracking-python
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: d4706e93470134751ade5f16e46a4323fd690f94
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84608644"
---
# <a name="quickstart-personalizer-client-library"></a>Краткое руководство. Клиентская библиотека Персонализатора

Отобразите персонализированное содержимое, используемое в этом кратком руководстве, с помощью службы "Персонализатор".

Приступите к работе с клиентской библиотекой Персонализатора. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач.

 * API ранжирования выбирает оптимальный вариант из доступных действий, основываясь на информации, которую вы предоставляете о содержимом и контексте в реальном времени.
 * API вознаграждения — вы определяете оценку вознаграждения в зависимости от потребностей вашего бизнеса, а затем отправляет его в Персонализатор с помощью этого API. Эта оценка может быть единичной величиной, например, 1 для "хорошо", а 0 — "плохо", или алгоритмом, который вы создаете на основе своих бизнес-потребностей.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/quickstart-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js SDK](./includes/quickstart-sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/quickstart-sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
>[Принцип работы Персонализатора](how-personalizer-works.md)

* [Что такое служба "Персонализатор"?](what-is-personalizer.md)
* [Где можно использовать Персонализатор](where-can-you-use-personalizer.md)
* [Устранение неполадок](troubleshooting.md)
* Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py).
