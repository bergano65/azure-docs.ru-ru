---
title: Устранение неполадок — QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker состоит из компонентов, размещенных в учетной записи Azure пользователя. Для отладки пользователям может потребоваться оперировать ресурсами Azure для QnA Maker или предоставить группе поддержки QnA Maker дополнительные сведения об их конфигурации.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/20/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b58a08c4cfa97356be4064456b2e9ec5cceccfbc
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876345"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Советы по устранению неполадок для поддержки службы и среды выполнения QnA Maker

QnAMaker включает ресурсы, размещенные в подписке Azure пользователя. Для отладки может потребоваться, чтобы пользователи работали с ресурсами Azure QnAMaker, или предоставить группе поддержки QnAMaker дополнительные сведения о настройке.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Как получить последние обновления для среды выполнения QnA Maker

Среда выполнения QnAMaker является частью службы приложений Azure, развернутой при [создании службы QnAMaker](./set-up-qnamaker-service-azure.md) в портал Azure. Периодически устанавливаются обновления для этой среды выполнения. QnA Maker служба приложений находится в режиме автоматического обновления после выпуска апреля 2019 для расширения сайта (версия 5 +). Это уже призвано позаботиться об НУЛЕВом времени простоя во время обновлений. 

Текущую версию можно проверить по адресу https://www.qnamaker.ai/UserSettings. Если версия старше версии 5. x, необходимо перезапустить службу приложений, чтобы применить последние обновления.

1. Перейдите к службе QnA Maker (группе ресурсов) на [портале Azure](https://portal.azure.com).

    ![Группа ресурсов Azure для QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Щелкните службу приложений и откройте раздел "Обзор".

     ![Служба приложений QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Перезапустите службу приложений. Эта операция должна потребовать нескольких секунд. Обратите внимание, что все зависимые приложения или программы-роботы, использующие эту службу QnAMaker, будут недоступны конечным пользователям в течение этого периода перезапуска.

    ![Перезапуск службы приложений QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Как получить имя узла службы QnA Maker
Имя узла службы QnA Maker удобно использовать для отладки при обращении в службу поддержки QnA Maker или на портал UserVoice. Имя узла — это URL-адрес в следующем формате: https:// *{HostName}* . azurewebsites.NET.
    
1. Перейдите к службе QnA Maker (группе ресурсов) на [портале Azure](https://portal.azure.com).

    ![Группа ресурсов Azure для QnAMaker на портале Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Выберите службу приложений, связанную с ресурсом QnA Maker. Как правило, имена совпадают.

     ![Выберите службу приложений QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. URL-адрес узла указан в разделе "Обзор".

    ![Имя узла QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Улучшите вопросы базы знаний с помощью активного обучения](./improve-knowledge-base.md)
