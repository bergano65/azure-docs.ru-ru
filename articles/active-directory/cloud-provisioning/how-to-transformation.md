---
title: Azure AD Connect преобразования подготовки облака
description: В этой статье описывается, как использовать преобразования для изменения сопоставлений атрибутов по умолчанию.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: ec12927b40096b7ff04fae6b7cbc69a7bc11e8f6
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75549301"
---
# <a name="transformations"></a>Преобразования

С помощью преобразования можно изменить поведение по умолчанию, определяющее, как атрибут синхронизируется с Azure Active Directory (Azure AD), используя подготовку облака.

Чтобы выполнить эту задачу, необходимо изменить схему, а затем повторно отправить ее через веб-запрос.

Дополнительные сведения об атрибутах подготовки облака см. в статье [Основные сведения о схеме Azure AD](concept-attributes.md).


## <a name="retrieve-the-schema"></a>Получение схемы
Чтобы получить схему, выполните действия, описанные в разделе [Просмотр схемы](concept-attributes.md#view-the-schema). 

## <a name="custom-attribute-mapping"></a>Сопоставление настраиваемых атрибутов
Чтобы добавить настраиваемое сопоставление атрибутов, выполните следующие действия.

1. Скопируйте схему в текст или редактор кода, например [Visual Studio Code](https://code.visualstudio.com/).
1. Выберите объект, который требуется обновить в схеме.

   ![Объект в схеме](media/how-to-transformation/transform1.png)</br>
1. Выберите код для `ExtensionAttribute3` в объекте User.

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
1. Измените код таким образом, чтобы атрибут Company был сопоставлен с `ExtensionAttribute3`.

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
 1. Скопируйте схему обратно в обозреватель Graph, измените **тип запроса** на " **Вставить**" и выберите **выполнить запрос**.

    ![Выполнение запроса](media/how-to-transformation/transform2.png)

 1. Теперь в портал Azure перейдите в раздел Настройка подготовки облака и выберите **перезапустить подготовку**.

    ![Перезапустить подготовку](media/how-to-transformation/transform3.png)

 1. Через некоторое время убедитесь, что атрибуты заполнены, выполнив следующий запрос в проводнике Graph: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`.
 1. Теперь вы увидите значение.

    ![Отображается значение](media/how-to-transformation/transform4.png)

## <a name="custom-attribute-mapping-with-function"></a>Сопоставление настраиваемых атрибутов с функцией
Для более расширенного сопоставления можно использовать функции, позволяющие манипулировать данными и создавать значения атрибутов в соответствии с потребностями Организации.

Чтобы выполнить эту задачу, выполните описанные выше действия и измените функцию, которая используется для создания окончательного значения.

Сведения о синтаксисе и примерах выражений см. [в разделе Написание выражений для сопоставления атрибутов в Azure Active Directory](reference-expressions.md).


## <a name="next-steps"></a>Дальнейшие действия 

- [Что собой представляет подготовка?](what-is-provisioning.md)
- [What is Azure AD Connect cloud provisioning?](what-is-cloud-provisioning.md) (Что такое подготовка облака Azure AD Connect?)
