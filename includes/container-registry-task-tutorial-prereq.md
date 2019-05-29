---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 6e0175173f17ae0958522517360b94ee80f3b2f9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66148984"
---
## <a name="prerequisites"></a>Предварительные требования

### <a name="get-sample-code"></a>Получение примера кода

В этом руководстве предполагается, что вы уже выполнили шаги в [предыдущем руководстве](../articles/container-registry/container-registry-tutorial-quick-task.md), создали вилку и клонировали пример репозитория. Если вы еще этого не сделали, выполните действия, описанные в разделе [Предварительные требования](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites) в предыдущем руководстве, прежде чем перед продолжить.

### <a name="container-registry"></a>Реестр контейнеров

Для выполнения заданий данного руководства необходимо иметь реестр контейнеров Azure в подписке Azure. Если у вас его нет, воспользуйтесь инструкциями в [предыдущем руководстве](../articles/container-registry/container-registry-tutorial-quick-task.md) или [кратком руководстве по созданию реестра контейнеров с помощью Azure CLI](../articles/container-registry/container-registry-get-started-azure-cli.md).

## <a name="create-a-github-personal-access-token"></a>Создание личного маркера доступа GitHub

Чтобы инициировать задачу при фиксации кода в репозитории Git, решению "Задачи ACR" требуется личный маркер доступа для доступа к репозиторию. Если у вас еще нет личного маркера доступа, выполните следующие действия, чтобы создать его на GitHub.

1. Перейдите на страницу создания PAT на GitHub по адресу https://github.com/settings/tokens/new.
1. Введите краткое **описание** маркера, например "ACR Task Demo".
1. Под флажком **repo** установите флажки **repo:status** и **public_repo**.

   ![Снимок экрана со страницей создания личного маркера доступа GitHub][build-task-01-new-token]

1. Нажмите кнопку **Generate token** (Создать маркер) (может появиться запрос на подтверждение пароля).
1. Скопируйте и сохраните созданный маркер в **защищенном расположении** (вы используете этот маркер при определении задачи в следующем разделе).

   ![Снимок экрана созданного личного маркера доступа на GitHub][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
