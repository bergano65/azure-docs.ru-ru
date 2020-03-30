---
title: Преобразования облачных преобразований Azure AD Connect
description: В этой статье описывается, как использовать преобразования для изменения отображения атрибутов по умолчанию.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: ec12927b40096b7ff04fae6b7cbc69a7bc11e8f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549301"
---
# <a name="transformations"></a>Преобразования

При преобразовании можно изменить поведение по умолчанию синхронизации атрибута с Active Directory Azure (Azure AD) с помощью облачной подготовки.

Для выполнения этой задачи необходимо отсеить схему, а затем повторно отправить ее через веб-запрос.

Для получения дополнительной информации о атрибутах подготовки облаков см. [Понимание схемы Azure AD.](concept-attributes.md)


## <a name="retrieve-the-schema"></a>Извлекай схему
Чтобы получить схему, выполните последующие шаги в [View the schema.](concept-attributes.md#view-the-schema) 

## <a name="custom-attribute-mapping"></a>Пользовательские отображения атрибутов
Чтобы добавить пользовательское отображение атрибутов, выполните следующие действия.

1. Копирование схемы в текст или редактор кода, таких как [Visual Studio Code](https://code.visualstudio.com/).
1. Найдите объект, который необходимо обновить в схеме.

   ![Объект в схеме](media/how-to-transformation/transform1.png)</br>
1. Найдите код `ExtensionAttribute3` для пользователя.

    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([extensionAttribute3])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[extensionAttribute3]",
                                                "name": "extensionAttribute3",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
1. Отспособите код таким образом, чтобы `ExtensionAttribute3`атрибут компании был отображены.

   ```
                                    {
                                        "defaultValue": null,
                                        "exportMissingReferences": false,
                                        "flowBehavior": "FlowWhenChanged",
                                        "flowType": "Always",
                                        "matchingPriority": 0,
                                        "targetAttributeName": "ExtensionAttribute3",
                                        "source": {
                                            "expression": "Trim([company])",
                                            "name": "Trim",
                                            "type": "Function",
                                            "parameters": [
                                                {
                                                    "key": "source",
                                                    "value": {
                                                        "expression": "[company]",
                                                        "name": "company",
                                                        "type": "Attribute",
                                                        "parameters": []
                                                    }
                                                }
                                            ]
                                        }
                                    },
   ```
 1. Скопируйте схему обратно в Graph Explorer, измените **тип запроса** на **PUT**и выберите **Выполнить запрос.**

    ![Выполнение запроса](media/how-to-transformation/transform2.png)

 1. Теперь на портале Azure перейдите в конфигурацию подготовки облака и выберите **подготовку Restart.**

    ![Перезапуск подготовки](media/how-to-transformation/transform3.png)

 1. Через некоторое время проверьте, что атрибуты заполняются, запустив следующий запрос в Graph Explorer: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`.
 1. Теперь вы должны увидеть значение.

    ![Значение отображается](media/how-to-transformation/transform4.png)

## <a name="custom-attribute-mapping-with-function"></a>Пользовательские отображения атрибутов с функцией
Для более продвинутого отображения можно использовать функции, позволяющие манипулировать данными и создавать значения для атрибутов в соответствии с потребностями организации.

Чтобы выполнить эту задачу, выполните предыдущие шаги, а затем отспособите функцию, используемую для построения конечного значения.

Для получения информации о синтаксисе и примерах выражений см. [Выражения Writing для отображения атрибутов в](reference-expressions.md)каталоге Azure Active.


## <a name="next-steps"></a>Дальнейшие действия 

- [Что собой представляет подготовка?](what-is-provisioning.md)
- [Что такое подготовка облака Azure AD Connect?](what-is-cloud-provisioning.md)
