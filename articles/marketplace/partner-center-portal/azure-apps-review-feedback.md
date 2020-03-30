---
title: Обработка отзывов отзывов для Azure Apps, предлагаемых на коммерческом рынке
description: Как обрабатывать отзывы о отзывах по приложениям Azure Apps для включения в список или продажи в Azure Marketplace, AppSource или через программу Cloud Solution Provider (CSP) с помощью портала Commercial Marketplace в партнерской центре Майкрософт.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 3932f3fc71dc6427b6cdf93d3a7bc58534a9981c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279799"
---
# <a name="handling-review-feedback"></a>Обработка отзывов по результатам проверки

В этой статье объясняется, как получить доступ к среде Azure DevOps, которая используется командой проверки Microsoft Azure Marketplace. Если в вашем предложении приложения Azure во время **проверки корпорацией Майкрософт** будут обнаружены критические ошибки, вы можете войти в эту систему и просмотреть подробные сведения об обнаруженных проблемах (отзывы по результатам проверки). Устранив все эти проблемы, повторно отправьте предложение, чтобы продолжить процесс публикации в Azure Marketplace. На следующей схеме показано, как процесс получения обратной связи связан с процессом публикации.

![Обзор процесса обратной связи](./media/review-feedback-process.png)

Как правило, проблемы проверки называются запросами на вытягивание. Каждый запрос на вытягивание связывается с элементом [Azure DevOps](https://azure.microsoft.com/services/devops/) (ранее эта служба называлась Visual Studio Team Services или VSTS), который содержит подробные сведения о проблеме. Следующее изображение отображает пример опыта Центра партнеров, если проблемы обнаружены во время отзывов. 

![Состояние публикации](./media/publishing-status.png)

PR, содержащий конкретные сведения о представлении, будет упомянут в ссылке "Посмотреть отчет о сертификации". В сложных ситуациях команда проверки и группа поддержки может дополнительно отправить вам письмо.

## <a name="azure-devops-access"></a>Доступ Azure DevOps

Все пользователи, имеющих доступ к роли "разработчика" в Partner Center, будут иметь доступ к просмотру элементов PR, упомянутых в отзывах о просмотре.

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

1. В разделе **обзора Майкрософт** в форме шагов публикации нажмите на PR-ссылку, чтобы запустить браузер и перейти на страницу **«Обзор»** (домашняя) для этого PR. Следующее изображение изображает пример главной страницы критического вопроса для предложения приложения образца Contoso. Эта страница содержит полезные сведения о проблемах, обнаруженных в приложении Azure при проверке.

    [![Потяните домашнюю страницу запроса](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Щелкните изображение, чтобы развернуть сведения.*

1. (Необязательно) На правой стороне окна, в разделе Политики, нажмите на сообщение проблемы (в этом примере: **Проверка политики не удалось),** чтобы исследовать низкоуровневые детали **проблемы,** в том числе связанные файлы журнала. Ошибки обычно отображаются в нижней части файлов журналов.
1. В левой части домашней страницы выберите в меню элемент **Files** (Файлы), чтобы получить список файлов с техническими ресурсами для этого предложения. Рецензенты Майкрософт обычно добавляют комментарии с описанием обнаруженных критических проблем. В следующем примере обнаружены две проблемы.

    [![Потяните домашнюю страницу запроса](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Щелкните изображение, чтобы развернуть сведения.*

1. Щелкните каждый узел комментария в дереве слева, чтобы перейти к соответствующему комментарию в контексте фрагмента кода. Исправьте исходный код приложения в командном проекте, чтобы устранить описанную проблему.

>[!Note]
>Вы не можете напрямую изменять технические ресурсы предложения в среде Azure DevOps команды проверки. Эта среда предоставляется издателям только для чтения исходного кода. Но вы можете оставить здесь ответ на комментарии для команды проверки Майкрософт.

   В следующем примере издатель проверил ошибку, исправил ее и ответил на первую проблему.

   ![Первый ответ с исправлением и комментарием](./media/first-comment-reply.png)

## <a name="next-steps"></a>Дальнейшие действия

Завершив исправление всех критических проблем, описанных в запросе на вытягивание по результатам проверки, необходимо [повторно опубликовать предложение приложения Azure](./create-new-azure-apps-offer.md#publish).
