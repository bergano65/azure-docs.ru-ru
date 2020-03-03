---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 33624ab800bd1155b52efbc05f317122a99bb479
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205833"
---
Перейдите на портал Azure и <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="создайте ресурс Распознавателя документов" target="_blank">create a new Form Recognizer resource <span class="docon docon-navigate-external x-hidden-focus"></span></a>. На панели **Создать** укажите следующие сведения:

|    |    |
|--|--|
| **имя**; | Описательное имя ресурса. Рекомендуется использовать описательное имя, например *Имя_распознавателя_документов*. |
| **Подписка** | Выберите подписку Azure, для которой предоставлен доступ. |
| **Расположение** | Расположение экземпляра Cognitive Service. Разные расположения могут увеличивать задержку, но не влияют на доступность среды выполнения ресурса. |
| **Ценовая категория** | Плата за ресурс будет зависеть от выбранной ценовой категории и показателей использования. Дополнительные сведения см. на [странице с ценами](https://azure.microsoft.com/pricing/details/cognitive-services/) API-интерфейсов.
| **Группа ресурсов** | [Группа ресурсов Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group), которая будет содержать ресурс. Вы можете создать новую группу или добавить к имеющейся группе. |

> [!IMPORTANT]
> Обычно при создании ресурсов Cognitive Service на портале Azure у вас есть возможность создать ключ подписки на несколько служб (используется в нескольких службах Cognitive Service) или ключ подписки на одну службу (используется только с одной определенной службой Cognitive Service). Но так как Распознаватель документов предоставляется в предварительной версии, на него не распространяется подписка на несколько служб, а подписку на одну службу можно создать, только используя ссылку в приветственном сообщении.

Когда ресурс "Распознаватель документов" будет развернут, найдите и выберите его в списке **Все ресурсы** на портале. Затем откройте вкладку **Быстрый запуск**, чтобы просмотреть данные подписки. Сохраните значения для **Key1** и **Конечная точка** во временном расположении. Эти значения вам понадобятся на следующих шагах.
