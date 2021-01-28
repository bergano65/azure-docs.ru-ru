---
title: Как настроить частную конечную точку — QnA Maker
description: Общие сведения о создании частных конечных точек, доступных в управляемом QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/12/2021
ms.openlocfilehash: 5a7ddcf25993ab4ce87bdc47c5e0ae32fa24b07f
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956593"
---
# <a name="private-endpoints"></a>Частные конечные точки

Частная конечная точка Azure — это сетевой интерфейс, который защищенно и надежно подключается к службе через Приватный канал Azure. Теперь QnA Maker предоставляет поддержку для создания частных конечных точек для Служба поиска Azure. Эта функция доступна в QnA Maker управляемых. 

Частные конечные точки предоставляются [частной ссылкой Azure](https://docs.microsoft.com/azure/private-link/private-link-overview)как отдельная служба. Дополнительные сведения о затратах см. на [странице с ценами.](https://azure.microsoft.com/pricing/details/private-link/) 

## <a name="prerequisites"></a>Предварительные требования
> [!div class="checklist"]
> * Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services/), прежде чем начинать работу.
> * QnA Maker [управляемый ресурс](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) , созданный в портал Azure. Запишите идентификатор Azure Active Directory, подписку и имя ресурса QnA, выбранное при создании ресурса.

## <a name="steps-to-enable-private-endpoint"></a>Действия по включению закрытой конечной точки
1. Назначьте роль *участия* в QnA Maker управляемой службе в экземпляре Служба поиска Azure. Для этой операции требуется доступ *владельца* к подписке. Перейдите на вкладку Identity (удостоверение) в ресурсе службы, чтобы получить удостоверение.
![Управляемое удостоверение службы](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-identity.png)

2. Добавьте удостоверение, указанное выше, в качестве *Contribute* , перейдя на вкладку Azure служба поиска IAM. ![ IAM-управляемая служба](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. Щелкните *добавить назначения ролей*, добавьте удостоверение и нажмите кнопку *сохранить*.
![Назначение управляемой роли](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. Теперь перейдите на вкладку " *сети* " в экземпляре Azure служба поиска и переключайте данные о подключении к конечной точке из *общедоступного* в *частный*. Эта операция выполняется в течение длительного процесса и может занять до 30 минут. 
![Управляемые сетевые службы поиска Azure](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. Перейдите на вкладку " *сети* " в разделе "управляемая служба QnA Maker" и в списке " *Разрешить доступ из*" выберите вариант *Выбранные сети и частные конечные точки* и нажмите кнопку *сохранить*. 
![Управляемый QnA Maker невторкинг](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-2.png)

Это позволит установить подключение к частной конечной точке между службой QnA Maker и экземпляром службы поиска Azure. Вы можете проверить подключение к частной конечной точке на вкладке " *Сетевые* подключения" экземпляра службы поиска Azure. После завершения всей операции можно использовать службу QnA Maker. 
![Управляемая сетевая служба](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)


## <a name="support-details"></a>Дополнительные сведения о поддержке
 * Вы не поддерживаете изменение Служба поиска Azure после включения частного доступа к службе QnAMaker. Если вы изменили Служба поиска Azure с помощью вкладки "Конфигурация" после включения частного доступа, служба QnAMaker станет непригодной для использования.
 * После установки подключения к частной конечной точке при переключении Служба поиска сети Azure в значение "Public" вы не сможете использовать службу QnAMaker. Для работы частного подключения к конечной точке Azure Служба поиска сети должны быть частными.
