---
title: Развертывание сопоставлений с помощью политик
description: Сведения о развертывании ассоциаций для настраиваемого поставщика с помощью службы политик Azure.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: 536b95eb07619d0ce2d02ec01e1f51ed52c1b5e1
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650478"
---
# <a name="deploy-associations-for-a-custom-provider-using-azure-policy"></a>Развертывание ассоциаций для настраиваемого поставщика с помощью политики Azure

Политики Azure можно использовать для развертывания ассоциаций, чтобы связать ресурсы с пользовательским поставщиком. В этой статье описывается встроенная политика, которая развертывает ассоциации и способ использования этой политики.

## <a name="built-in-policy-to-deploy-associations"></a>Встроенная политика для развертывания ассоциаций

Развертывание ассоциаций для настраиваемого поставщика — это встроенная политика, которую можно использовать для развертывания ассоциации, чтобы связать ресурс с пользовательским поставщиком. Политика принимает три параметра:

- ИДЕНТИФИКАТОР настраиваемого поставщика — идентификатор ресурса настраиваемого поставщика, с которым необходимо связать ресурсы.
- Типы ресурсов для связывания. Эти типы ресурсов представляют собой список типов ресурсов, которые будут связаны с настраиваемым поставщиком. С помощью одной политики можно связать с пользовательским поставщиком несколько типов ресурсов.
- Префикс имени Ассоциации — эта строка является префиксом, добавляемым к имени создаваемого ресурса ассоциации. Значение по умолчанию — "Деплойедбиполици".

Политика использует оценку DeployIfNotExists. Он запускается после того, как поставщик ресурсов обработал запрос на создание или обновление ресурса, и вычисление вернуло код состояния успеха. После этого ресурс связывания будет развернут с помощью развертывания шаблона.
Дополнительные сведения о связях см. в статье подключение [ресурсов настраиваемых поставщиков Azure](./concepts-resource-onboarding.md) .

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Использование встроенной политики «развертывание ассоциаций» 

### <a name="prerequisites"></a>Технические условия
Если пользовательскому поставщику требуются разрешения области действия политики, чтобы выполнить действие, развертывание политики ресурса связи не будет работать без предоставления разрешений.

### <a name="policy-assignment"></a>Назначение политики
Чтобы использовать встроенную политику, создайте назначение политики и назначьте ассоциации развертывания для настраиваемой политики поставщика. Затем политика определит ресурсы, которые не соответствуют требованиям, и развернет сопоставление для этих ресурсов.

![Назначить встроенную политику](media/concepts-built-in-policy/assign-builtin-policy-customprovider.png)

## <a name="getting-help"></a>Получение справки

Если у вас возникли вопросы по разработке поставщиков настраиваемых ресурсов Azure, попробуйте запрашивать их на [Stack overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Подобный вопрос, возможно, уже был задан, поэтому перед его публикацией сначала проверьте наличие ответа. Чтобы быстрее получить ответ, добавьте к вопросу тег ```azure-custom-providers```!

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнаете об использовании встроенной политики для развертывания ассоциаций. Дополнительные сведения см. в следующих статьях:

- [Основные понятия: адаптация ресурсов настраиваемых поставщиков Azure](./concepts-resource-onboarding.md)
- [Учебник. Адаптация ресурсов с помощью настраиваемых поставщиков](./tutorial-resource-onboarding.md)
- [Руководство. Создание настраиваемых действий и ресурсов в Azure](./tutorial-get-started-with-custom-providers.md)
- [Краткое руководство. Создание настраиваемого поставщика ресурсов и развертывание настраиваемых ресурсов](./create-custom-provider.md)
- [Как добавлять настраиваемые действия в REST API Azure](./custom-providers-action-endpoint-how-to.md)
- [Как добавлять пользовательские ресурсы в REST API Azure](./custom-providers-resources-endpoint-how-to.md)
