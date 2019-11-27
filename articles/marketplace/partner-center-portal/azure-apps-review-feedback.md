---
title: Обработка отзывов о предложениях по проверке приложений Azure в коммерческом магазине
description: Сведения о том, как с помощью портала коммерческого рынка в центре партнеров Майкрософт работать с отзывом о просмотре приложений Azure для получения списка или продажи в Azure Marketplace, AppSource или с помощью программы поставщика облачных решений (CSP).
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 7406a6e35929e3df5d342affbcb4b740efa0cab8
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281224"
---
# <a name="handling-review-feedback"></a>Обработка отзывов по результатам проверки

В этой статье объясняется, как получить доступ к среде Azure DevOps, которая используется командой проверки Microsoft Azure Marketplace. Если в вашем предложении приложения Azure во время **проверки корпорацией Майкрософт** будут обнаружены критические ошибки, вы можете войти в эту систему и просмотреть подробные сведения об обнаруженных проблемах (отзывы по результатам проверки). Устранив все эти проблемы, повторно отправьте предложение, чтобы продолжить процесс публикации в Azure Marketplace. На следующей схеме показано, как процесс получения обратной связи связан с процессом публикации.

![Отзыв о процессе](./media/review-feedback-process.png)

Как правило, проблемы проверки называются запросами на вытягивание. Каждый запрос на вытягивание связывается с элементом [Azure DevOps](https://azure.microsoft.com/services/devops/) (ранее эта служба называлась Visual Studio Team Services или VSTS), который содержит подробные сведения о проблеме. На следующем рисунке показан пример интерфейса центра партнеров, если проблемы обнаружены во время проверок. 

![Состояние публикации](./media/publishing-status.png)

Запрос на вытягивание, содержащий конкретные сведения об отправке, будет упомянут в ссылке "Просмотр отчета о сертификации". В сложных ситуациях команда проверки и группа поддержки может дополнительно отправить вам письмо.

## <a name="azure-devops-access"></a>Доступ к DevOps Azure

Все пользователи с доступом к роли "Разработчик" в центре партнеров получат доступ для просмотра элементов запроса на вытягивание, на которые ссылается отзыв о проверке.

<!---
To view the PR items referenced in review feedback, publishers must first be granted proper authorization. Otherwise, new publishers receive a 401 - Not Authorized response page when trying to view PRs. To request access to this Azure DevOps repository, perform the following steps:

1. Collect the following information:
    * Your publisher name and ID
    * Offer type (Azure app), offer name, and SKU ID
    * The pull request link, for example: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`. This URL can be retrieved from the notification message or the address of the 401 response page.
    * The email address(es) of the individuals from your publishing organization that you want access granted to. This list should include the owner address(es) you provided when registering as a publisher on the Cloud Partner Portal (or Partner Center??).
1. Create a support incident. In the title bar of the Cloud Partner Portal (??), select the **Help** button, then choose **Support** from the menu. Your default web browser should launch and navigate to the Microsoft new support incident page. (You may have to sign in first.)
1. Specify the **Problem type** as **marketplace onboarding** and **Category** as **Access problem**, then select **Start request**.

    ![Create an incident](./media/create-an-incident.png)

1. In **Step 1 of 2** page, supply your contact information and select **Continue**.
1. In **Step 2 of 2** page, specify an incident title (for example Request Azure DevOps access) and supply the information you collected in the first step (above). Read and accept the agreement, then select **Submit**.

If the incident creation was successful, a confirmation page is displayed. Save the confirmation information on this page for your reference. The Microsoft Support Team should reply to your access request within a few business days. --->

## <a name="reviewing-the-pull-request"></a>Просмотр запроса на вытягивание

Используйте следующую процедуру, чтобы изучить проблемы, описанные в запросе на вытягивание.

1. В разделе **Microsoft Review** в форме шагов публикации щелкните ссылку PR, чтобы открыть браузер и перейти на страницу **Обзор** (Главная) для этого запроса на вытягивание. На следующем рисунке показан пример домашней страницы критической проблемы для предложения примера приложения Contoso. Эта страница содержит полезные сведения о проблемах, обнаруженных в приложении Azure при проверке.

    
   [домашней страницы запроса на вытягивание![](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)  <br/> *Щелкните изображение, чтобы развернуть сведения.*

1. Используемых В правой части окна в разделе **политики**щелкните сообщение о выпуске (в этом примере — **Проверка политики не удалось**), чтобы исследовать сведения о низком уровне проблемы, включая связанные файлы журналов. Ошибки обычно отображаются в нижней части файлов журналов.
1. В левой части домашней страницы выберите в меню элемент **Files** (Файлы), чтобы получить список файлов с техническими ресурсами для этого предложения. Рецензенты Майкрософт обычно добавляют комментарии с описанием обнаруженных критических проблем. В следующем примере обнаружены две проблемы.

    
   [домашней страницы запроса на вытягивание![](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)  <br/> *Щелкните изображение, чтобы развернуть сведения.*

1. Щелкните каждый узел комментария в дереве слева, чтобы перейти к соответствующему комментарию в контексте фрагмента кода. Исправьте исходный код приложения в командном проекте, чтобы устранить описанную проблему.

>[!Note]
>Вы не можете напрямую изменять технические ресурсы предложения в среде Azure DevOps команды проверки. Эта среда предоставляется издателям только для чтения исходного кода. Но вы можете оставить здесь ответ на комментарии для команды проверки Майкрософт.

   В следующем примере издатель проверил ошибку, исправил ее и ответил на первую проблему.

   ![Первый ответ с исправлением и комментарием](./media/first-comment-reply.png)

## <a name="next-steps"></a>Дополнительная информация

Завершив исправление всех критических проблем, описанных в запросе на вытягивание по результатам проверки, необходимо [повторно опубликовать предложение приложения Azure](./create-new-azure-apps-offer.md#publish).
