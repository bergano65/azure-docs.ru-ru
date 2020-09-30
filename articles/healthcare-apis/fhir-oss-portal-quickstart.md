---
title: 'Портал Azure: Развертывание сервера FHIR с открытым кодом для Azure — Azure API для FHIR'
description: В этом кратком руководстве показано, как развернуть сервер FHIR Майкрософт с открытым кодом с помощью портала Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 57ab6bca820c4c25a9a56e4a801aa7d917d317ec
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90978589"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-portal"></a>Краткое руководство. Развертывание сервера FHIR с открытым кодом с помощью портала Azure

В этом кратком руководстве показано, как развернуть сервер FHIR с открытым кодом в Azure с помощью портала Azure. В [репозитории ПО с открытым кодом](https://github.com/Microsoft/fhir-server) будут использоваться простые ссылки для развертывания.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="github-open-source-repository"></a>Репозиторий ПО с открытым кодом на GitHub

Перейдите на [страницу развертывания GitHub](https://github.com/Microsoft/fhir-server/blob/master/docs/DefaultDeployment.md) и найдите кнопки Deploy to Azure (Развернуть в Azure).

>[!div class="mx-imgBorder"]
>![Страница развертывания ПО с открытым кодом](media/quickstart-oss-portal/deployment-page-oss.png)

Нажмите кнопку развертывания, после чего откроется портал Azure.

## <a name="fill-in-deployment-parameters"></a>Укажите параметры развертывания

Создайте новую группу ресурсов и присвойте ей имя. Здесь присутствуют еще два обязательных параметра: имя службы и пароль администратора SQL.

>[!div class="mx-imgBorder"]
>![Параметры настраиваемого развертывания](media/quickstart-oss-portal/deployment-custom-parameters.png)

Введя сведения, можно начать развертывание.

## <a name="validate-fhir-server-is-running"></a>Проверка работы сервера FHIR

По завершении развертывания можно перейти в браузере по адресу `https://SERVICENAME.azurewebsites.net/metadata`, чтобы получить отчет о возможностях. Первый ответ сервера может поступить через минуту или около того.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если группа ресурсов и связанные с ней ресурсы вам больше не нужны, их можно удалить. Для этого выберите группу ресурсов с подготовленными ресурсами, щелкните **Удалить группу ресурсов**, а затем подтвердите имя удаляемой группы ресурсов.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве показано, как развернуть сервер FHIR Майкрософт для Azure с открытым кодом в своей подписке. Чтобы узнать, как получить доступ к API FHIR с помощью Postman, перейдите к руководству по Postman.
 
>[!div class="nextstepaction"]
>[Получение доступа к API FHIR с помощью Postman](access-fhir-postman-tutorial.md)