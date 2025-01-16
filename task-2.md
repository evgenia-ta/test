# Задача 2

<details>

<summary>

## Текст задачи

</summary>

Перенести существующее описание REST API в формат OpenAPI и дополнить его, опираясь на вводные данные.

**Ожидаемый результат:** OpenAPI спецификация и дополнительными материалами по желанию.

**Язык:** английский. 

**Вводные данные:**
Пользователь может приобретать один план обслуживания (или не приобретать ни одного). Набор планов можно изменять через API, и не все из них доступны каждому пользователю, т. к. привязаны к его провайдеру. Подписка на план меняет статус пользователя на “signing” до момента подтверждения. Кроме того, модель пользователя дополняется новым аттрибутом “sub_status” со значениями “signed”, “unsigned” и “absent” — эти статусы изменять запросами нельзя. В настоящий момент количество планов для каждого партнёра ограниченно mapping’ом из существующих типов (“type” пользователя, кроме “guest” и “admin”), которые можно задавать в запросе через запятую. Планы можно создавать, изменять и удалять. Запросы на предоставление информации поддерживают опциональный идентификатор и pagination. Для запросов изменения планов требуется Basic-авторизация.

**Имеющееся описание:**

### User

**Method:** GET

**Endpoint:** /api/external/user

**Request:** 

| Parameter | Description | Values |
| --- | --- | --- |
| user_id | (optional) Unique user identifier. | String. Seven characters long. Examples: “3453255” |
|from | (optional) Pagination start timestamp. | Integer. Timestamp in nanoseconds.|
|to | (optional) Pagination end timestamp. | Integer. Timestamp in nanoseconds.|
|limit | (optional) Limit to records returned. | Integer. From 10 to 1000. Default: 1000|
|provider_id | (optional) Unique identifier of the partner a user bound to. | String. Four characters long. Examples: “2323”|

**Response:**

| Parameter | Description | Values |
| --- | --- | --- |
| user_id | Unique user identifier. | String. Seven characters long. Example: “3453255”|
| provider_id | Unique identifier of the partner a user bound to. | String. Four characters long. Example: “2323”|
| status | User service state. | String. Values: “active”, “inactive”, “idle”|
| type | User type. | String. “basic”, “guest”, “company”, “advanced”, “admin”|
| zone | Region of residence. | String. Two letters of lower case (Alpha-2 code, ISO 3166). Examples: “ru”, “us”, “gb”|

**Example:**

curl -X GET {host}/api/external/user?user_id=5478485&limit=100

…

### Partner

**Method:** GET

**Endpoint:** /api/partner

**Request:**

| Parameter | Description | Values |
| --- | --- | --- |
| provider_id | (required) Unique partner identifier. | String. Four characters long. Example: “2323” |
| status | (optional) Partner operation state. | String. Values: “active”, “stopped” |

**Response:**
| Parameter | Description | Values |
| --- | --- | --- |
| provider_id | Unique partner identifier. | String. Four characters long. Example: “2323” |
| status | Partner operation state. | String. Values: “active”, “stopped” |

**Example:** 

curl -X GET -H "Authorization: Basic YXBkdXJlcjphcGlkl2Q=" {host}/api/partner?provider_id=6578

…



</details>

<details>

<summary>

## Комментарии

</summary>

После перевода исходного описания в формат OpenAPI я сделала следующее:

-	Добавила в ответ `GET /api/external/user`:

    - атрибут `plan_id` c id плана, приобретенного пользователем;

    - еще одно значение атрибута `status` = `signing`;

    - новый атрибут `sub_status`.

- Добавила новые методы работы с планами: `POST`, `PUT`, `DELETE` с необходимостью Basic-авторизации.

- В описании не упоминался `GET` для планов, но я посчитала, что он тоже может быть нужен.

Дополнительные комментарии:

- Если какой-то информации не хватало (например, о перечне атрибутов для плана), подходила к вопросу творчески и придумывала недостающее сама :) В реальной рабочей задаче, конечно, предварительно уточнила бы информацию доступными способами.

- Ответы для краткости я указывала только "успешные".

- Замечания по следующей фразе:

   > В настоящий момент количество планов для каждого партнёра ограниченно mapping’ом из существующих типов (“type” пользователя, кроме “guest” и “admin”), которые можно задавать в запросе через запятую.

   - Из фразы ясно, что на количество доступных планов для каждого партнёра влияют типы пользователей у этого партнера. Я предположила, что можно создать по несколько планов, доступных разным типам, но как точно работает ограничение, осталось неясным. Этот вопрос в реальной задаче я бы уточнила отдельно.

   - Предположила, что через запятую типы можно передавать в запросе на просмотр планов.

- Показалось странным, что на вход метода для просмотра партнера `GET /api/partner` можно подавать статус с учетом того, что там обязателен `partner_id`. Здесь я бы заподозрила какую-то ошибку и сообщила команде.

- В этой задаче упоминается тип `admin`, которого не было в [Задаче 1](task-1.md). Я не стала включать его в Задачу 1 или убирать из Задачи 2, но несоответствие заметила.

</details>

## Результат

### OpanAPI specification (YAML)
          
```yaml
openapi: 3.0.0
info:
  title: Product API
  description: API for managing users, partners, and plans
  version: 1.0.0
servers:
  - url: https://host.com/api/v3
tags:
  - name: user
  - name: partner
  - name: plan
paths:
  /api/external/user:
    get:
      tags:
        - user    
      summary: Get users
      description: Get a list of users with optional filters and pagination  
      parameters:
        - name: user_id
          in: query
          description: Unique identifier of the user
          required: false
          schema:
            type: string
            minLength: 7
            maxLength: 7
            example: "3453255"
        - name: from
          in: query
          description: Pagination start timestamp (in nanoseconds)
          required: false
          schema:
            type: integer
            example: 1672525800000000000
        - name: to
          in: query
          description: Pagination end timestamp (in nanoseconds)
          required: false
          schema:
            type: integer
            example: 1672526800000000000
        - name: limit
          in: query
          description: Limit on records returned
          required: false
          schema:
            type: integer
            minimum: 10
            maximum: 1000
            default: 1000
        - name: provider_id
          in: query
          description: Unique identifier of the partner to which the user is bound
          required: false
          schema:
            type: string
            minLength: 4
            maxLength: 4
            example: "2323"
      responses:
        '200':
          description: Success
          content:
            application/json:
              schema:
                type: array
                items:
                  type: object
                  properties:
                    user_id:
                      type: string
                      description: Unique identifier of the user
                      example: "3453255"
                    provider_id:
                      type: string
                      description: Unique identifier of the partner to which the user is bound
                      example: "2323"
                    plan_id:
                      type: string
                      description: Unique identifier of the user's plan
                      example: "1234"
                    status:
                      type: string
                      description: User service state
                      enum: [active, inactive, idle, signing]
                      example: "active"
                    sub_status:
                      type: string
                      description: User plan purchase state
                      enum: [signed, unsigned, absent]
                      example: "signed"
                    type:
                      type: string
                      description: User type
                      enum: [basic, guest, company, advanced, admin]
                      example: "basic"
                    zone:
                      type: string
                      description: Region of residence (ISO 3166-1 Alpha-2, lowercase)
                      example: "ru"
  /api/partner:
    get:
      tags:
        - partner    
      summary: Get a partner
      description: Get information about a specific partner
      security:
        - basicHttpAuthentication: []
      parameters:
        - name: provider_id
          in: query
          description: Unique identifier of the partner
          required: true
          schema:
            type: string
            minLength: 4
            maxLength: 4
            example: "2323"
        - name: status
          in: query
          description: Partner operation state
          required: false
          schema:
            type: string
            enum: [active, stopped]
            example: "active"
      responses:
        '200':
          description: Success
          content:
            application/json:
              schema:
                type: object
                properties:
                  provider_id:
                    type: string
                    description: Unique identifier of the partner
                    example: "2323"
                  status:
                    type: string
                    description: Partner operation state
                    enum: [active, stopped]
                    example: "active"
  /api/plan:
    get:
      tags:
        - plan   
      summary: Get plans
      description: Get a list of plans with optional filters and pagination
      parameters:
        - name: plan_id
          in: query
          description: Unique identifier of the plan
          required: false
          schema:
            minLength: 4
            maxLength: 4
            type: string
            example: "1234"
        - name: provider_id
          in: query
          description: Unique identifier of the partner to which the plan is bound
          required: false
          schema:
            minLength: 4
            maxLength: 4
            type: string
            example: "2323"
        - name: type
          in: query
          description: List of user types the plan is available to, passed as comma-separated values
          required: false
          schema:
            type: array
            items:
              type: string
              enum: [basic, company, advanced]
          example: ["basic", "company"]
        - name: from
          in: query
          description: Pagination start timestamp (in nanoseconds)
          required: false
          schema:
            type: integer
            example: 1672525800000000000
        - name: to
          in: query
          description: Pagination end timestamp (in nanoseconds)
          required: false
          schema:
            type: integer
            example: 1672526800000000000
        - name: limit
          in: query
          description: Limit on records returned
          required: false
          schema:
            type: integer
            minimum: 10
            maximum: 100
            default: 100
      responses:
        '200':
          description: Success
          content:
            application/json:
              schema:
                type: array
                items:
                  type: object
                  properties:
                    plan_id:
                      type: string
                      description: Unique identifier of the plan
                      example: "1234"
                    name:
                      type: string
                      description: Plan name
                      example: "Standard"
                    description:
                      type: string
                      description: Plan description
                      example: "A standard plan for basic users"
                    provider_id:
                      type: string
                      description: Unique identifier of the partner to which the plan is bound
                      example: "2323"
                    type:
                      type: string
                      description: User type the plan is available to
                      enum: [basic, company, advanced]
                      example: "basic"
    post:
      tags:
        - plan
      summary: Create a plan
      description: Add a new plan for a specific partner and user type
      security:
        - basicHttpAuthentication: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                plan_id:
                  type: string
                  description: Unique identifier of the plan
                  minLength: 4
                  maxLength: 4
                  example: "123"
                name:
                  type: string
                  description: Plan name
                  example: "Premium"
                description:
                  type: string
                  description: Plan description
                  example: "A subscription plan offering premium options"
                provider_id:
                  type: string
                  description: Unique identifier of the partner to which the plan is bound
                  minLength: 4
                  maxLength: 4
                  example: "2323"
                type:
                  type: string
                  description: User type the plan is available to
                  enum: [basic, company, advanced]
                  example: "basic"
      responses:
        '201':
          description: Success
    put:
      tags:
        - plan
      summary: Update an existing plan
      description: Modify information about an existing plan
      security:
        - basicHttpAuthentication: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                plan_id:
                  type: string
                  description: Unique identifier of the plan to update
                  example: "1234"
                name:
                  type: string
                  description: The updated plan name
                  example: "Premium New"
                description:
                  type: string
                  description: The updated plan description
                  example: "A premium plan offering advanced features"
                provider_id:
                  type: string
                  description: The updated identifier of the partner to which the plan is bound
                  example: "2323"
                type:
                  type: string
                  description: The updated user type the plan is available to
                  enum: [basic, company, advanced]
      responses:
        '200':
          description: Success
    delete:
      tags:
        - plan
      summary: Delete a plan
      description: Remove a plan from the system
      security:
        - basicHttpAuthentication: []
      parameters:
        - name: plan_id
          in: query
          description: Unique identifier of the plan to delete
          required: true
          schema:
            type: string
            example: "1234"
      responses:
        '200':
          description: Success
components:
  securitySchemes:
    basicHttpAuthentication:
      description: Basic HTTP Authentication
      type: http
      scheme: Basic
```
