---
title: Устранение распространенных ошибок
description: Узнайте, как устранять неполадки при создании, назначении и удалении чертежей, таких как нарушения политик и функции параметров схемы.
ms.date: 01/15/2020
ms.topic: troubleshooting
ms.openlocfilehash: 7306e344a479008a87164a954c4444d375950b0b
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157089"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Устранение неполадок с помощью службы Azure Blueprint

При создании, назначении или удалении схем можно столкнуться с ошибками. В этой статье описываются различные ошибки, которые могут возникнуть, и пути их решения.

## <a name="finding-error-details"></a>Поиск сведений об ошибке

Многие ошибки возникают в результате назначения схем области. Если назначение завершается сбоем, схема предоставляет сведения о неудачном развертывании. Эти сведения указывают на проблемы, которые нужно исправить, чтобы последующее развертывание было выполнено успешно.

1. Выберите **Все службы** в левой области. Найдите и выберите пункт **Схемы**.

1. Выберите **назначенные** схемы на странице слева и используйте поле поиска для фильтрации назначений в схеме, чтобы найти неудачное назначение. Можно также отсортировать таблицу назначений по столбцу **Состояние подготовки**, чтобы сгруппировать все неудачные назначения.

1. Щелкните правой кнопкой мыши проект с состоянием _Failed_ или выберите пункт **Просмотр сведений о назначении**.

1. В верхней части страницы назначения схемы отображается красный баннер, предупреждающий, что назначение завершилось сбоем. Щелкните в любом месте баннера, чтобы получить дополнительные сведения.

Довольно часто ошибка вызвана артефактом, а не схемой в целом. Если артефакт создает Key Vault, а Политика Azure предотвращает его создание, последующее назначение завершится ошибкой.

## <a name="general-errors"></a>Общие ошибки

### <a name="policy-violation"></a>Сценарий: нарушение политики

#### <a name="issue"></a>Проблема

Не удалось выполнить развертывание шаблона из-за нарушения политики.

#### <a name="cause"></a>Причина

Политика может конфликтовать с развертыванием по целому ряду причин:

- создаваемый ресурс ограничен политикой (обычно в связи с ограничениями SKU или расположения);
- в развертывании устанавливаются поля, настроенные политикой (распространено при использовании тегов).

#### <a name="resolution"></a>Разрешение

Измените схему, чтобы в сведениях об ошибке она не сталкивалась с политиками. Если это невозможно, следует изменить область назначения политики так, чтобы схема больше не сталкивалась с политикой.

### <a name="escape-function-parameter"></a>Сценарий: параметр схемы является функцией

#### <a name="issue"></a>Проблема

Параметры схемы, которые являются функциями, обрабатываются перед передачей в артефакты.

#### <a name="cause"></a>Причина

Передача параметра схемы, которая использует функцию (например `[resourceGroup().tags.myTag]`) для результатов артефакта, приводит к тому, что обработанный результат функции устанавливается на артефакт вместо динамической функции.

#### <a name="resolution"></a>Разрешение

Чтобы передать функцию в качестве параметра, направьте всю строку с `[` так, чтобы параметр схемы был похож на `[[resourceGroup().tags.myTag]`. Escape-символ заставляет схемы обрабатывать значение как строку при обработке. Схемы затем помещают функцию на артефакт, позволяя ему быть динамичным, как и ожидалось. Дополнительные сведения см. [в разделе синтаксис и выражения в шаблонах Azure Resource Manager](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="delete-errors"></a>Удалить ошибки

### <a name="assign-delete-timeout"></a>Сценарий: время ожидания удаления назначения

#### <a name="issue"></a>Проблема

Удаление назначения схемы не завершено.

#### <a name="cause"></a>Причина

При удалении назначение схемы может оставаться в состоянии, отличном от терминала. Это состояние возникает, когда ресурсы, созданные при назначении схемы, ожидают удаления или не возвращают код состояния в проектные проекты Azure.

#### <a name="resolution"></a>Разрешение

Назначения схемы в состоянии, отличном от терминала, автоматически помечаются как **неудачные** после истечения _6 часов_ . После того, как время ожидания изменило состояние назначения схемы, удаление может быть повторено.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

- Получите ответы от экспертов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
- Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).
- Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.