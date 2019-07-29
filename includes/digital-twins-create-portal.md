---
title: включение файла
description: включение файла
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 07/26/2019
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: de81700496089ffc6cfdba8e02eaa4e14200d16d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68601375"
---
1. Войдите на [портале Azure](https://portal.azure.com).

1. В области слева выберите **Создать ресурс**. Выполните поиск по фразе **digital twins** и выберите **Digital Twins**. Нажмите кнопку **Создать**, чтобы начать развертывание.

   ![Выбранные элементы для создания экземпляра Digital Twins](./media/create-digital-twins-portal/create-digital-twins.png)

1. В области **Digital Twins** введите следующие сведения:
   * **Имя ресурса**: Задайте уникальное имя для экземпляра Digital Twins.
   * **Подписка**: Выберите подписку, в которой будет создан экземпляр Digital Twins. 
   * **Группа ресурсов.** Выберите или создайте [группу ресурсов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) для экземпляра Digital Twins.
   * **Расположение.** Выберите ближайшее к вашим устройствам расположение.

     ![Область Digital Twins с введенными сведениями](./media/create-digital-twins-portal/create-digital-twins-param.png)

1. Проверьте сведения, указанные для Digital Twins, и щелкните **Создать**. Создание экземпляра Digital Twins может занять несколько минут. Ход создания можно отслеживать на панели **уведомлений**.

1. Откройте область **Общие сведения** для созданного экземпляра Digital Twins. Обратите внимание на ссылку, которая отображается под элементом **API управления**.

   URL-адрес **API управления** имеет следующий вид: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger`. По этому URL-адресу находится документация по REST API для Azure Digital Twins, которая относится к созданному экземпляру. См. сведения об [использовании Azure Digital Twins ](../articles/digital-twins/how-to-use-swagger.md), чтобы получить представление о том, как изучать и применять эту документацию по API.

    Измените URL-адрес **API управления** следующим образом: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Этот измененный URL-адрес станет базовым адресом для доступа вашего приложения к экземпляру Digital Twins. Скопируйте измененный URL-адрес во временный файл. Этот URL-адрес понадобится вам в следующих разделах.

    ![API управления](./media/create-digital-twins-portal/digital-twins-management-api.png)