---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 897f2b728dc068b09849d4f48f899b8630a87a51
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004010"
---
Перейдите на портал Azure и <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="создайте ресурс Распознавателя документов" target="_blank">create a new Form Recognizer resource <span class="docon docon-navigate-external x-hidden-focus"></span></a>. На панели **Создать** укажите следующие сведения:

|    |    |
|--|--|
| **имя**; | Описательное имя ресурса. Рекомендуется использовать описательное имя, например *Имя_распознавателя_документов*. |
| **Подписка** | Выберите подписку Azure, для которой предоставлен доступ. |
| **Расположение** | Расположение экземпляра Cognitive Service. Разные расположения могут увеличивать задержку, но не влияют на доступность среды выполнения ресурса. |
| **Ценовая категория** | Плата за ресурс будет зависеть от выбранной ценовой категории и показателей использования. Дополнительные сведения см. на [странице с ценами](https://azure.microsoft.com/pricing/details/cognitive-services/) API-интерфейсов.
| **Группа ресурсов** | [Группа ресурсов Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group), которая будет содержать ресурс. Вы можете создать новую группу или добавить к имеющейся группе. |

> [!NOTE]
> Обычно при создании ресурсов Cognitive Service на портале Azure у вас есть возможность создать ключ подписки на несколько служб (используется в нескольких службах Cognitive Service) или ключ подписки на одну службу (используется только с одной определенной службой Cognitive Service). Но в настоящее время Распознаватель документов не включен в подписку на несколько служб.

Когда ресурс "Распознаватель документов" будет развернут, найдите и выберите его в списке **Все ресурсы** на портале. Ключ и конечная точка будут располагаться на странице ключа и конечной точки ресурса в разделе управления ресурсами. Сохраните их во временном расположении, прежде чем продолжать работу.