---
title: Общие сведения о создании приложений Power BI — Azure Marketplace
description: В этой статье описаны общие действия по публикации Power BI приложения для Microsoft AppSource. Также предоставляются технические и бизнес-требования, которым должно соответствовать приложение Power BI для публикации в коммерческом магазине.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 8f050a43cbdf4ab29df55cd5526eb231c301b271
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732680"
---
# <a name="power-bi-app-creation-overview"></a>Общие сведения о создании приложения Power BI

> [!IMPORTANT]
> Мы перемещая Управление предложениями Power BI приложений из Портал Cloud Partner в центр партнеров. Пока ваши предложения не будут перенесены, следуйте инструкциям в [Power BI предложениях для приложений](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-power-bi-offer) , чтобы портал Cloud Partner для управления вашими предложениями.

В этой статье объясняется, как опубликовать Power BI приложение в Microsoft [AppSource](https://appsource.microsoft.com/). Power BIное приложение упаковывает настраиваемое содержимое, включая наборы данных, отчеты и панели мониторинга. Затем можно использовать приложение с другими Power BIными платформами с помощью AppSource, выполнить корректировки и настройки, разрешенные разработчиком, и подключить их к собственным данным.

## <a name="publishing-benefits"></a>Преимущества публикации

Преимущества публикации на коммерческом рынке:

- Повышение уровня компании с помощью торговой марки Майкрософт.
- Возможно достижение более чем 100 000 000 пользователей Office 365 и Dynamics 365 в AppSource и более чем в 200 000 организациях через Azure Marketplace.
- Получайте высококачественные интересы из этих рынков.
- Попросите своих служб, продвигаемых группами «Майкрософт» и «телепродажи».

## <a name="overview"></a>Обзор

:::image type="content" source="media/power-bi-app-publishing-steps.png" alt-text="Общие сведения о действиях по публикации приложения Power BI" border="false":::

Ниже приведены основные этапы публикации.

1. Создайте приложение в Power BI. Вы получите ссылку на установку пакета, которая является основным техническим ресурсом для предложения. Перед созданием предложения в центре партнеров отправьте тестовый пакет в предварительную рабочую среду. Дополнительные сведения см. в разделе [что такое Power BI приложений?](https://docs.microsoft.com/power-bi/service-template-apps-overview).
2. Добавьте маркетинговые материалы, такие как официальное название, описание и логотипы.
3. Включите документы о юридической и технической поддержке предложения, такие как условия использования, политика конфиденциальности, политика поддержки и Справка пользователя.
4. Создание предложения. Используйте центр партнеров для изменения сведений, включая описание предложения, маркетинговые материалы, юридические сведения, сведения о поддержке и спецификации активов.
5. Отправьте его для публикации.
6. Отслеживайте процесс в центре партнеров, где команда адаптации AppSource тестирует, проверяет и удостоверяет Ваше приложение.
7. После сертификации проверьте приложение в тестовой среде и выпустите его. Он будет перечисляться в AppSource (он "переходит в активное").
8. В Power BI отправьте пакет в рабочую среду. Дополнительные сведения см. [в разделе Управление выпуском Power BI приложения](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release).

## <a name="before-you-begin"></a>Подготовка к работе

Ознакомьтесь с приведенными ниже ссылками, которые содержат шаблоны, советы и примеры.

- [Создание приложения Power BI](https://docs.microsoft.com/power-bi/service-template-apps-create)
- [Советы по разработке приложения Power BI](https://docs.microsoft.com/power-bi/service-template-apps-tips)
- [Примеры](https://docs.microsoft.com/power-bi/service-template-apps-samples)

## <a name="requirements"></a>Requirements (Требования)

Для публикации в коммерческом магазине предложение Power BI приложения должно соответствовать следующим техническим и бизнес-требованиям.

### <a name="technical-requirements"></a>Технические требования

Основной необходимый технический ресурс — это [Power BI приложение](https://go.microsoft.com/fwlink/?linkid=2028636). Это коллекция основных наборов данных, отчетов или панелей мониторинга. Он также включает дополнительные подключенные службы и внедренные наборы данных, которые ранее назывались [пакетом содержимого](https://docs.microsoft.com/power-bi/service-organizational-content-pack-introduction). Дополнительные сведения о разработке этого типа приложений см. в разделе [что такое приложения Power BI?](https://go.microsoft.com/fwlink/?linkid=2028636).

#### <a name="get-an-installation-web-address"></a>Получение веб-адреса установки

Вы можете создать только приложение Power BI в среде [Power BI](https://powerbi.microsoft.com/) .

1. Выполните вход с помощью [Power BI Pro лицензии](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro).
2. Создание и тестирование приложения в Power BI.
3. Когда вы получаете веб-адрес установки приложения, добавьте его на страницу **технической настройки** в центре партнеров.

После создания и тестирования приложения в Power BI сохраните веб-адрес установки приложения, так как он понадобится для [создания Power BI предложения приложения](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer).

### <a name="business-requirements"></a>Бизнес-требования

Бизнес-требования включают в себя процедурные, договорные и юридические обязательства. Необходимо сделать следующее:

- Быть зарегистрированным издателем коммерческого рынка. Если вы не зарегистрировались, выполните действия, описанные в разделе [Станьте коммерческим издателем Marketplace](https://docs.microsoft.com/azure/marketplace/become-publisher).
- Укажите содержимое, удовлетворяющее критериям предложения, которые будут перечислены в AppSource. Дополнительные сведения см [. в разделе приложение для перечисления в AppSource. Вот как это](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how)делать.
- Примите условия и следуйте [заявлению о конфиденциальности Майкрософт](https://privacy.microsoft.com/privacystatement).

## <a name="next-steps"></a>Дальнейшие шаги

- [Создание предложения Power BI приложения в центре партнеров](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer)