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
ms.date: 01/14/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 94a3dfd9a3560321d7e2753ccd385fb1a5323107
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559932"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Советы по устранению неполадок для поддержки службы и среды выполнения QnA Maker
QnA Maker состоит из компонентов, размещенных в учетной записи Azure пользователя. Для отладки пользователям может потребоваться оперировать ресурсами Azure для QnA Maker или предоставить группе поддержки QnA Maker дополнительные сведения об их конфигурации.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Как получить последние обновления для среды выполнения QnA Maker
Среда выполнения QnA Maker является частью развернутой службы приложений Azure в случае [создания службы QnA Maker](./set-up-qnamaker-service-azure.md) на портале Azure. Периодически устанавливаются обновления для этой среды выполнения. QnA Maker служба приложений находится в режиме автоматического обновления POST (версия 5 и более апреля 2019) для расширения сайта. Это уже призвано позаботиться об НУЛЕВом времени простоя во время обновлений. Текущую версию можно проверить по адресу https://www.qnamaker.ai/UserSettings. Если версия старше версии 5. x, необходимо перезапустить службу приложений, чтобы применить последние обновления для установки QnAMaker.

1. Перейдите к службе QnA Maker (группе ресурсов) на [портале Azure](https://portal.azure.com).

    ![Группа ресурсов Azure для QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Щелкните службу приложений и откройте раздел "Обзор".

     ![Служба приложений QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Перезапустите службу приложений. Эта операция должна потребовать нескольких секунд. Обратите внимание на то, что нисходящие приложения и боты, созданные на основе этой службы QnA Maker, будут недоступны для пользователей в течение периода ее перезапуска.

    ![Перезапуск службы приложений QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Как получить имя узла службы QnA Maker
Имя узла службы QnA Maker удобно использовать для отладки при обращении в службу поддержки QnA Maker или на портал UserVoice. Имя узла — это URL-адрес вида https:// *{имя_узла}* .azurewebsites.net.
    
1. Перейдите к службе QnA Maker (группе ресурсов) на [портале Azure](https://portal.azure.com).

    ![Группа ресурсов Azure для QnAMaker на портале Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Щелкните службу приложений.

     ![Выберите службу приложений QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. URL-адрес узла указан в разделе "Обзор".

    ![Имя узла QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Улучшите вопросы базы знаний с помощью активного обучения](./improve-knowledge-base.md)
