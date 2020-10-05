---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 49feedaa087a89b2dfc5d90c7230b7abf23ed1ba
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88815759"
---
Перейдите на портал Azure и <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="создайте ресурс Распознавателя документов" target="_blank">create a new Form Recognizer resource <span class="docon docon-navigate-external x-hidden-focus"></span></a>. На панели **Создать** укажите следующие сведения:

|    |    |
|--|--|
| **имя**; | Описательное имя ресурса. Рекомендуется использовать описательное имя, например *Имя_распознавателя_документов*. |
| **Подписка** | Выберите подписку Azure, для которой предоставлен доступ. |
| **Расположение** | Расположение экземпляра Cognitive Service. Разные расположения могут увеличивать задержку, но не влияют на доступность среды выполнения ресурса. |
| **Ценовая категория** | Плата за ресурс будет зависеть от выбранной ценовой категории и показателей использования. Дополнительные сведения см. на [странице с ценами](https://azure.microsoft.com/pricing/details/cognitive-services/) API-интерфейсов.
| **Группа ресурсов** | [Группа ресурсов Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group), которая будет содержать ресурс. Вы можете создать новую группу или добавить к имеющейся группе. |

> [!NOTE]
> Обычно при создании ресурсов Cognitive Service на портале Azure у вас есть возможность создать ключ подписки на несколько служб (используется в нескольких службах Cognitive Service) или ключ подписки на одну службу (используется только с одной определенной службой Cognitive Service). Но в настоящее время Распознаватель документов не включен в подписку на несколько служб.

Когда ресурс "Распознаватель документов" будет развернут, найдите и выберите его в списке **Все ресурсы** на портале. Ключ и конечная точка будут располагаться на странице ключа и конечной точки ресурса в разделе управления ресурсами. Сохраните их во временном расположении, прежде чем продолжать работу.
