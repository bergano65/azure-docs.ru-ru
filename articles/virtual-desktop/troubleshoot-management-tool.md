---
title: Инструмент управления виртуальным рабочим столом Windows - Azure
description: Как устранить проблемы с помощью инструмента управления персональным компьютером Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 11/09/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9d07ba42e83d9eec071ab047e9e1e92bac1f1411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127486"
---
# <a name="troubleshoot-the-windows-virtual-desktop-management-tool"></a>Устранение неполадок со средством управления Виртуального рабочего стола Windows

В этой статье описаны проблемы, которые могут возникнуть при развертывании инструмента управления windows Virtual Desktop и способы их устранения.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>Ошибка: службы инструментов управления настроены, но автоматизированная настройка не удается

При успешной настройке служб для инструмента управления, но автоматизированной настройки не удается, вы увидите это сообщение об ошибке:

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

Это обычно означает одну из следующих двух вещей:

- Пользователь имеет разрешения владельца на подписку и глобального админа на уровне арендаторов, но они не могут войти в Azure.
- Настройки учетной записи пользователя включены в многофакторную аутентификацию.

Чтобы устранить эту проблему, выполните следующие действия:

1. Убедитесь, что пользователь, созданный для основного имени пользователя Azure Active Directory, имеет уровень подписки "Вкладчик".
2. Войти на <portal.azure.com> с учетной записью UPN, чтобы проверить настройки учетной записи и убедиться, что многофакторная аутентификация не на. Если он включен, выключите его.
3. Посетите страницу Windows Virtual Desktop Consent и убедитесь, что сервер и клиентские приложения имеют согласие.
4. Просмотрите учебник [«Развертывание инструмента управления»,](manage-resources-using-ui.md) если проблема продолжается, и передислоцируетинструмент.

## <a name="error-job-with-specified-id-already-exists"></a>Ошибка: Работа с указанным идентификатором уже существует

Если пользователь видит сообщение об ошибке "Работа с указанным идентификатором уже существует", это потому, что он не указал уникальное имя в параметре "Имя приложения" при развертывании шаблона.

Чтобы исправить это, передислоцировать инструмент управления с параметром "Имя приложения" заполнены.

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>Запрос с задержкой согласия при открытии инструмента управления

При развертывании инструмента управления запрос согласия может не открыться сразу. Это означает, что загрузка web-приложения Azure занимает больше времени, чем обычно. Запрос должен отображаться после загрузки Azure Web.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>Пользователь не может развернуть инструмент управления в восточном регионе США

Если клиент устанавливает регион на Восток США, он не может развернуть инструмент управления.

Чтобы исправить это, разместите инструмент управления в другом регионе. Перераспределение инструмента в другом регионе не должно повлиять на пользовательский опыт.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте о треках эскалации в [обзоре, обратной связи и поддержке.](troubleshoot-set-up-overview.md)
- Узнайте, как сообщить о проблемах с инструментами виртуального рабочего стола Windows в [ARM Templates для удаленных настольных служб.](https://github.com/Azure/RDS-Templates/blob/master/README.md)
- Для обзора устранения неполадок Windows Virtual Desktop и треков эскалации [см.](troubleshoot-set-up-overview.md)
- Чтобы узнать, как развернуть инструмент управления, [см.](manage-resources-using-ui.md)