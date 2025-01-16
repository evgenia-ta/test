# Задача 3

<details>

<summary>

## Текст задачи

</summary>

Проверить текст на предмет наличия грамматических, стилистических и фактических ошибок, а также ошибок оформления.

**Ожидаемый результат:** исправленный или полностью переписанный текст.

**Текст:**

_Plans provide product clients with a variety of service options defined within a preconfigured score._
 

_Please note that shortly after the feature implementation, your customer service flow and solutions may undergo some alignment due to an extensive system update. To be more precise, a user record is now supplemented by additional properties, which are described in the latest version of our API reference. For example, the users can be transferred to the 'idle' state before they confirm purchasing a plan._

_The capacity of plans per provider is unlimited, so feel free to create as many of them as necessary and customize them to up to a desirable extent. Despite that, plans - merely surface well-known user attributes complying completely with the corresponding state flaw._

</details>

<details>

<summary>

## Комментарии

</summary>

В первую очередь требовалось определить аудиторию и цели текста. Можно предположить, что это анонс новой функциональности/release notes для сотрудников компаний-партнеров. Цель текста — уведомить, упомянуть о плюсах, кратко ознакомить и дать ссылки на дополнительные, более подробные материалы.

К дополнительным материалам я отнесла: 

- отдельную overview статью про работу с планами, информацией об ограничениях, полезными кейсами, etc.

- релевантные страницы API-документации.

\+ Некоторые замеченные ошибки:

1. >  within a preconfigured score 

   Вероятно, здесь опечатка и по смыслу должно быть scope. Но кажется, что и понятие preconfigured scope звучит расплывчато. Если здесь идет речь о том, что планы для клиентов преднастраиваются партнером, то можно упомянуть это более явно. 

2. > Please note that shortly after the feature implementation, your customer service flow and solutions may undergo some alignment due to an extensive system update. To be more precise, ...

   Есть вопросы по смыслу: что имеется в виду под "may undergo some alignment", в чем конкретно это будет проявляться.

      - Если речь идет только о том, что добавятся новые поля и новый статус у клиента, то стоит убрать расплывчатую фразу и явно перечислить изменения.

      - Если же мы хотим акцентировать, что эти изменения могут повлечь какие-то действия со стороны партнера и нужно более обстоятельно об этом рассказать, то нужно написать об этом более однозначно. Для такого случая я предложила текст в виде примечания в конце.

3. > the users can be transferred to the 'idle' state before they confirm purchasing a plan.

   Есть противоречие с [Задачей 2](task-2.md), где говорилось про статус 'signing', а не 'idle'.

4. > The capacity of plans per provider is unlimited

   Также есть противоречие с Задачей 2, где упоминалось ограниченное количество планов для провайдера. В этой задаче я кратко упомянула о наличии ограничения и дала отсылку к overview-статье.

5. > Despite that, plans - merely surface well-known user attributes complying completely with the corresponding state flaw.

   Здесь опечатка в слове flaw. Я бы также уточнила технические детали, которые лежат под этой фразой, — чтобы переписать ее, убрать неопределенность.

</details>

## Результат

We’ve added plans functionality to let you offer flexible sets of service options. Create and customize plans to better meet your clients' needs.

**Key updates:**

- You can [create, update, delete, and retrieve](...) plans using the API.

- The [user record](...) now includes the `sub_status` property to track subscription status. 

- Users are automatically set to the `signing` status until they confirm the plan purchase.

The number of plans per provider is limited. For more details, see our [overview page](...).

> [!NOTE]
>  After the feature is implemented, your customer service processes and solutions may require adjustments due to a system update. To determine if any changes are necessary, see [Possible adjustments](...).
