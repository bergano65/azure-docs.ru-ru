---
title: Устранение неполадок — QnA Maker
titlesuffix: Azure Cognitive Services
description: QnA Maker состоит из компонентов, размещенных в учетной записи Azure пользователя. Для отладки пользователям может потребоваться оперировать ресурсами Azure для QnA Maker или предоставить группе поддержки QnA Maker дополнительные сведения об их конфигурации.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 065b6551098a39fb737b7eface17d78b111d31b6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074151"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Советы по устранению неполадок для поддержки службы и среды выполнения QnA Maker
QnA Maker состоит из компонентов, размещенных в учетной записи Azure пользователя. Для отладки пользователям может потребоваться оперировать ресурсами Azure для QnA Maker или предоставить группе поддержки QnA Maker дополнительные сведения об их конфигурации.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Как получить последние обновления для среды выполнения QnA Maker
Среда выполнения QnA Maker является частью развернутой службы приложений Azure в случае [создания службы QnA Maker](./set-up-qnamaker-service-azure.md) на портале Azure. Периодически устанавливаются обновления для этой среды выполнения. Чтобы применить последние обновления к конфигурации QnA Maker, необходимо перезапустить службу приложений.
1. Перейдите к службе QnA Maker (группе ресурсов) на [портале Azure](https://portal.azure.com).

    ![Группа ресурсов Azure для QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Щелкните службу приложений и откройте раздел "Обзор".

     ![Служба приложений QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Перезапустите службу приложений. Эта операция должна потребовать нескольких секунд. Обратите внимание на то, что нисходящие приложения и боты, созданные на основе этой службы QnA Maker, будут недоступны для пользователей в течение периода ее перезапуска.

    ![Перезапуск службы приложений QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Как получить имя узла службы QnA Maker
Имя узла службы QnA Maker удобно использовать для отладки при обращении в службу поддержки QnA Maker или на портал UserVoice. Имя узла — это URL-адрес вида https://*{имя_узла}*.azurewebsites.net.
    
1. Перейдите к службе QnA Maker (группе ресурсов) на [портале Azure](https://portal.azure.com).

    ![Группа ресурсов Azure для QnAMaker на портале Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Щелкните службу приложений.

     ![Выберите службу приложений QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. URL-адрес узла указан в разделе "Обзор".

    ![Имя узла QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Использование API QnA Maker](./upgrade-qnamaker-service.md)
