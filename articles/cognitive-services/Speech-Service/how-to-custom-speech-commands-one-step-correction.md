---
title: Инструкции. Добавление одноэтапного исправления в пользовательскую команду (Предварительная версия) — служба речи
titleSuffix: Azure Cognitive Services
description: В этой статье объясняется, как реализовать исправления одного этапа для команды в пользовательских командах.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456457"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Как добавить одношаговое исправление в настраиваемую команду (Предварительная версия)

В этой статье вы узнаете, как добавить в команду одноразовое подтверждение.

Одношаговое исправление используется для обновления только что выполненной команды.

Т. е. Если вы просто настроили оповещение, вы можете изменить свое внимание и обновить время оповещения.

- Входные данные: задать оповещение для завтрашнего дня в полдень
- Выходные данные: "ОК, оповещение установлено для 12/06/2019 12:00:00"
- Ввод: нет, завтра по адресу 13:00-14:30
- Выходные данные: "ОК

Помните, что это означает, что у разработчика есть механизм обновления сигнала в серверном приложении.

## <a name="prerequisites"></a>Технические условия

Необходимо выполнить действия, описанные в следующих статьях:

- [Краткое руководство. Создание настраиваемой команды (Предварительная версия)](./quickstart-custom-speech-commands-create-new.md)
- [Краткое руководство. Создание настраиваемой команды с параметрами (Предварительная версия)](./quickstart-custom-speech-commands-create-parameters.md)
- [Как добавить подтверждение в настраиваемую команду (Предварительная версия)](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>Добавление расширенных правил для одношаговой корректировки 

Чтобы продемонстрировать одношаговую коррекцию, добавим команду **сеталарм** , созданную в окне [подтверждения](./how-to-custom-speech-commands-confirmations.md).
 
1. Добавьте расширенное правило для обновления предыдущего оповещения. 

    Это правило попросит пользователя подтвердить дату и время оповещения и ожидает подтверждения (да/нет) для следующей активации.

   | Параметр               | Рекомендуемое значение                                                  | Description                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Имя правила             | Обновить предыдущий будильник                                            | Имя, описывающее назначение правила.          |
   | Условия            | Упдателасткомманд & обязательный параметр-DateTime                | Условия, определяющие, когда может выполняться правило    |   
   | Действия               | Спичреспонсе-"-обновление предыдущего оповещения до {DateTime}"       | Действие, выполняемое, если условие правила имеет значение true |
   | Состояние после выполнения | Полная команда                                                 | Состояние пользователя после включения                   |

1. Переместите только что созданное правило в верхнюю часть расширенных правил (прокрутите правило на панели и щелкните стрелку вверх).
   > [!div class="mx-imgBorder"]
   > ![добавить проверку диапазона](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> В реальном приложении в разделе действия этого правила вы также отправите действие клиенту или вызовите конечную точку HTTP для обновления сигнала в системе.

## <a name="try-it-out"></a>Попробуйте в деле

Выберите панель "тестирование" и попробуйте несколько взаимодействий.

- Входные данные: задать оповещение для завтрашнего дня в полдень
- Выходные данные: "вы действительно хотите установить оповещение для 12/07/2019 12:00:00?"
- Входные данные: Да
- Выходные данные: "ОК, оповещение установлено для 12/07/2019 12:00:00"
- Ввод: нет, завтра по адресу 13:00-14:30
- Выходные данные: "обновление предыдущего оповещения до 12/07/2019 13:00:00"
