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
ms.openlocfilehash: 7fa119db0c974c93aff667060d153b21b8de16bb
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87843493"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-portal"></a>Краткое руководство. Развертывание сервера FHIR с открытым кодом с помощью портала Azure

В этом кратком руководстве показано, как развернуть сервер FHIR с открытым кодом в Azure с помощью портала Azure. В [репозитории ПО с открытым кодом](https://github.com/Microsoft/fhir-server) будут использоваться простые ссылки для развертывания.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="github-open-source-repository"></a>Репозиторий ПО с открытым кодом на GitHub

Перейдите на [страницу развертывания GitHub](https://github.com/Microsoft/fhir-server/blob/master/docs/DefaultDeployment.md) и найдите кнопки Deploy to Azure (Развернуть в Azure).

![Страница развертывания ПО с открытым кодом](media/quickstart-oss-portal/deployment-page-oss.png)

Нажмите кнопку развертывания, после чего откроется портал Azure.

## <a name="fill-in-deployment-parameters"></a>Укажите параметры развертывания

Создайте новую группу ресурсов и присвойте ей имя. Еще один обязательный параметр — это имя службы.

![Параметры настраиваемого развертывания](media/quickstart-oss-portal/deployment-custom-parameters.png)

Обратите внимание, что при развертывании исходный код будет извлекаться непосредственно из репозитория ПО с открытым исходным кодом на GitHub. Если вы создали вилку репозитория, вы можете указать на нее и на конкретную ветвь.

Введя сведения, можно начать развертывание.

## <a name="validate-fhir-server-is-running"></a>Проверка работы сервера FHIR

По завершении развертывания можно перейти в браузере по адресу `https://SERVICENAME.azurewebsites.net/metadata`, чтобы получить отчет о возможностях. Первый ответ сервера может поступить через минуту или около того.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если группа ресурсов и связанные с ней ресурсы вам больше не нужны, их можно удалить. Для этого выберите группу ресурсов с подготовленными ресурсами, щелкните **Удалить группу ресурсов**, а затем подтвердите имя удаляемой группы ресурсов.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве показано, как развернуть сервер FHIR Майкрософт для Azure с открытым кодом в своей подписке. Чтобы узнать, как получить доступ к API FHIR с помощью Postman, перейдите к руководству по Postman.
 
>[!div class="nextstepaction"]
>[Получение доступа к API FHIR с помощью Postman](access-fhir-postman-tutorial.md)