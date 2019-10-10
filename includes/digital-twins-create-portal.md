---
title: включение файла
description: включение файла
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/24/2019
ms.custom: include file
ms.openlocfilehash: 7b2df437833f270a6e102257693426f4cc65b9d2
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949746"
---
1. Войдите на [портале Azure](https://portal.azure.com).

1. В области слева выберите **Создать ресурс**. Выполните поиск по фразе **digital twins** и выберите **Digital Twins**. Нажмите кнопку **Создать**, чтобы начать развертывание.

   [![Выбранные элементы для создания экземпляра Digital Twins](./media/create-digital-twins-portal/create-digital-twins.png)](./media/create-digital-twins-portal/create-digital-twins.png#lightbox)

1. В области **Digital Twins** введите следующие сведения:
   * **Имя ресурса**: Задайте уникальное имя для экземпляра Digital Twins.
   * **Подписка**: Выберите подписку, в которой будет создан экземпляр Digital Twins. 
   * **Группа ресурсов.** Выберите или создайте [группу ресурсов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) для экземпляра Digital Twins.
   * **Расположение.** Выберите ближайшее к вашим устройствам расположение.

     [![Область Digital Twins с введенными сведениями](./media/create-digital-twins-portal/create-digital-twins-param.png)](./media/create-digital-twins-portal/create-digital-twins-param.png#lightbox)

1. Проверьте сведения, указанные для Digital Twins, и щелкните **Создать**. Создание экземпляра Digital Twins может занять несколько минут. Ход создания можно отслеживать на панели **уведомлений**.

1. Откройте область **Общие сведения** для созданного экземпляра Digital Twins. Обратите внимание на ссылку, которая отображается под элементом **API управления**.

   URL-адрес **API управления** имеет следующий вид: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger`. По этому URL-адресу находится документация по REST API для Azure Digital Twins, которая относится к созданному экземпляру. См. сведения об [использовании Azure Digital Twins ](../articles/digital-twins/how-to-use-swagger.md), чтобы получить представление о том, как изучать и применять эту документацию по API.

    Измените URL-адрес **API управления** следующим образом: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Этот измененный URL-адрес станет базовым адресом для доступа вашего приложения к экземпляру Digital Twins. Скопируйте измененный URL-адрес во временный файл. Этот URL-адрес понадобится вам в следующих разделах.

    [![API управления](./media/create-digital-twins-portal/digital-twins-management-api.png)](./media/create-digital-twins-portal/digital-twins-management-api.png#lightbox)