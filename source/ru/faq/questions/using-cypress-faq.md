---
layout: toc-top
title: Using Cypress
containerClass: faq
---

## {% fa fa-angle-right %} Как я могу получить текст элемента?

Команды Cypress возвращают объекты jQuery, поэтому вы можете вызывать их методы.

Если вы пытаетесь проверить текстовое содержимое элемента:

```javascript
cy.get('div').should('have.text', 'foobarbaz')
```

Если вы хотите получить текст перед выполнением проверки:

```javascript
cy.get('div').should(($div) => {
  const text = $div.text()

  expect(text).to.match(/foo/)
  expect(text).to.include('foo')
  expect(text).not.to.include('bar')
})
```

Если вам необходимо преобразовать текст в число перед тем, как проверить, что он больше 10:

```javascript
cy.get('div').invoke('text').then(parseFloat).should('be.gt', 10)
```

Если вам необходимо сравнить текст до и после:

```javascript
cy.get('div').invoke('text').then((text1) => {
  // дополнительные действия

  // нажимаем кнопку, которая изменяет текст в div-е
  cy.get('button').click()

  // снова берём текст из div и сравниваем предыдущее значение
  // с текущим
  cy.get('div').invoke('text').should((text2) => {
    expect(text1).not.to.eq(text2)
  })
})
```

Метод jQuery `.text()` автоматически вызывает `elem.textContent` под капотом. Если вы хотите вместо этого использовать `innerText`, то вы можете сделать следующее:

```javascript
cy.get('div').should(($div) => {
  // доступ к нативному DOM-элементу
  expect($div.get(0).innerText).to.eq('foobarbaz')
})
```

Это эквивалент методу Selenium-а `getText()`, который возвращает внутренний текст видимого элемента.

## {% fa fa-angle-right %} Как я могу получить значение поля ввода?

Команды Cypress возвращают объекты jQuery, поэтому вы можете вызывать их методы.

Если вы пытаетесь проверить значение поля ввода:

```javascript
// сделайте проверку на значение
cy.get('input').should('have.value', 'abc')
```

Если вы хотите модифицировать текст перед тем, как сделать проверку:

```javascript
cy.get('input').should(($input) => {
  const val = $input.val()

  expect(val).to.match(/foo/)
  expect(val).to.include('foo')
  expect(val).not.to.include('bar')
})
```

Если вам необходимо сравнить текст до и после:

```javascript
cy.get('input').invoke('val').then((val1) => {
  // дополнительные действия

  // нажимаем кнопку, которая изменяет текст в поле ввода
  cy.get('button').click()

  // снова берём текст из поля ввода и сравниваем предыдущее значение
  // с текущим
  cy.get('input').invoke('val').should((val2) => {
    expect(val1).not.to.eq(val2)
  })
})
```

## {% fa fa-angle-right %} Как я могу проверить значение или состояние одного элемента по отношению к другому?

Наше {% url 'руководство по переменным и алиасам' variables-and-aliases %} содержит примеры как сделать именно это.

## {% fa fa-angle-right %} Могу ли я сохранить значение атрибута в константу или переменную для последующего использования?

Да, и есть несколько способов это сделать. Первый способ - хранить значение или ссылку при помощи {% url '`замыканий`' variables-and-aliases#Closures %}.
В целом пользователи считают, что необходимо хранить значение в `const`, `var` или `let`. Cypress рекомендует делать это только с мутабельными объектами (которые изменяют состояние).

За примером обратитесь, пожалуйста, к нашему {% url 'гайду о переменных и алиасах' variables-and-aliases %}.

## {% fa fa-angle-right %} Как я могу получить нативную DOM-ссылку на элемент, который я нашёл при помощи Cypress?

Cypress делает обёртку для элементов jQuery, поэтому получить нативный элемент из обёртки вы можете при помощи команды {% url "`.then()`" then %}.

```javascript
cy.get('button').then(($el) => {
  $el.get(0)
})
```

## {% fa fa-angle-right %} Как мне обработать ситуацию, когда элемент не существует?

То, о чём вы спрашиваете, называется conditional testing (тестирование по условию) и control flow (порядок исполнения).

Пожалуйста, прочтите наше обширное {% url 'руководство по тестированию по условию' conditional-testing %}, в котором объяснены все детали.

## {% fa fa-angle-right %} Как сделать в Cypress ожидание появления элементов в DOM?

{% note info Важно помнить %}
Команды, которые ищут что-то в DOM, будут автоматически {% url "повторяться" retry-ability %} и перед тем как упасть будут ожидать появления искомых элементов.
{% endnote %}

Cypress предлагает множество надёжных способов {% url 'запросить что-то в DOM' introduction-to-cypress#Querying-Elements %} и все они обёрнуты в логику повтор-и-ожидание.

Другой способ дождаться появления элемента в DOM - это `таймауты`. Команды Cypress по умолчанию будут ждать 4 секунды, однако, большинство команд Cypress имеют {% url 'опции задания таймаутов' configuration#Timeouts %}. Таймауты также могут быть сконфигурированы глобально или через командную строку.

В {% url 'некоторых случаях' interacting-with-elements#Visibility %}, к вашему DOM-элементу нельзя будет обратиться. Cypress даёт вам мощную опцию {%url '`{force:true}`' interacting-with-elements#Forcing %}, которую вы можете передать в большинство команд.

**Пожалуйста, прочтите** наше {% url 'введение в ключевые концепции Cypress' introduction-to-cypress %}. Это исчерпывающее руководство поможет вам понять как тестировать в Cypress.

## {% fa fa-angle-right %} Как мне сделать ожидание загрузки приложения?

Нам много раз задавали этот вопрос. Ответы могут быть разными в зависимости от того, как ваше приложение ведёт себя и в каких условиях вы тестируете. Вот примеры наиболее часто встречающихся вариантов этого вопроса.

**_Как мне узнать, что моя страница загрузилась?_**

Когда вы загружаете ваше приложение при помощи `cy.visit()`, Cypress будет ждать наступления события `load`. Команда {% url '`cy.visit()`' visit#Usage %} загружает удалённую страницу и не прекращает выполнение до тех пор, пока все внешние ресурсы не будут загружены. Так как мы ожидаем, что ваши приложения могут иметь разную скорость загрузки, по умолчанию эта команда ожидает 60000мс. Если вы перейдёте по некорректному url или на {% url 'второй уникальный домен' web-security#Same-superdomain-per-test %}, Cypress залогирует длинное, но зато понятное сообщение об ошибке.

**_Если я использую CI, как мне убедиться, что мой сервер стартовал?_**

Мы рекомендуем эти отличные модули для этого случая:

* {% url '`wait-on`' https://www.npmjs.com/package/wait-on %}
* {% url '`start-server-and-test`' https://github.com/bahmutov/start-server-and-test %}

**_Как мне дождаться исполнения запросов?_**

Вам, определённо, нужно использовать команду {% url '`cy.server()`' server#Syntax %}, определить пути при помощи {% url '`cy.route()`' route#Syntax %}, создать {% url '`алиасы`' variables-and-aliases#Aliases %} для этих путей перед тем, как переходить по ним, и _тогда_ вы сможете явно указать Cypress при помощи {% url '`cy.wait()`' wait#Syntax %} выполнения каких операций вы хотите дождаться. **Нет никакого магического способа дождаться всех ваших XHRs или Ajax запросов.** Из-за асинхронной природы этих запросов Cypress не может интуитивно понять, как их дождаться. Вы должны определить пути и однозначно сказать Cypress каких запросов вы хотите дождаться.

## {% fa fa-angle-right %} Могу ли я проверить HTML-элемент `<head>`?

Да, конечно, можете. В процессе выполнения тестов в Test Runner, вы можете просматривать объект `window.document` в открытой консоли используя {% url '`cy.document()`' document %}. Также вы можете сделать проверку элемента `<head>`. Обратите внимание на пример.

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
  <meta http-equiv="Content-Security-Policy" content="default-src 'self'">
  <meta name="description" content="Такое метовое описание">
  <title>Проверка содержимого HEAD</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
</head>
<body>
</body>
</html>
```

```js
describe('Мета-информация вашего документа', () => {
  beforeEach(() => {
    cy.visit('/')
  })

  it('смотрим внутрь тега head, используя `cy.document()`', () => {
    // эта команда вернёт целиком объект window.document
    // если вы нажмёте на  DOCUMENT в логе команд,
    // то это выведет #document целиком в консоль
    cy.document()
  })

  // или сделайте проверку любой мета-информации в элементе head

  it('смотрим внутрь тега <title>', () => {
    cy.get('head title')
      .should('contain', 'Проверка содержимого HEAD')
  })

  it('смотрим внутрь тега <meta> и ищем описание', () => {
    cy.get('head meta[name="description"]')
      .should('have.attr', 'content', 'Такое метовое описание')
  })
})
```

## {% fa fa-angle-right %} Могу ли я проверить, что валидационное сообщение на HTML-форме показывается, когда ввод невалидный?

Конечно, можете.

```javascript
it('проверка валидационного сообщения для некорректного ввода', () => {
  cy.get('input:invalid').should('have.length', 0)
  cy.get('[type="email"]').type('not_an_email')
  cy.get('[type="submit"]').click()
  cy.get('input:invalid').should('have.length', 1)
  cy.get('[type="email"]').then(($input) => {
    expect($input[0].validationMessage).to.eq('А я, вообще-то, ожидал email!')
  })
})
```


## {% fa fa-angle-right %} Могу ли я замедлить скорость сети при помощи Cypress?

Вы можете замедлить скорость сети в панели Developer Tools, вкладка Network. В дополнение, вы можете добавить свои собственные настройки, выбрав **Custom > Add** в выпадающем меню на вкладке Network.

На данный момент мы не предлагаем способа эмулировать такое поведение в процессе `cypress run`.

## {% fa fa-angle-right %} Могу ли я использовать новый ES7 async / await синтаксис?

Нет. Наше API разрабатывалось без учёта, что это возможно. Это не ограничение Cypress - это вполне осознанное и важное проектное решение.

Async / await - это синтаксический сахар вокруг промисов, а команды Cypress - это сочетание промисов и стримов.

Если вы заинтересовались, пожалуйста, прочтите:

- Наше {% url 'вводное руководство по Cypress' introduction-to-cypress#Commands-Are-Asynchronous %}, которое объясняет, как работаю Команды API
- Наше {% url 'руководство по переменным и алиасам' variables-and-aliases %}, которое рассказывает о паттернах работы с асинхронным кодом

## {% fa fa-angle-right %} How do I select or query for elements if my application uses dynamic classes or dynamic IDs?

Don't use classes or ID's. You add `data-*` attributes to your elements and target them that way.

Read more about the {% url 'best practices for selecting elements here' best-practices#Selecting-Elements %}.

## {% fa fa-angle-right %} I want to run tests only within one specific folder. How do I do this?

You can specify which test files to run during {% url "`cypress run`" command-line#cypress-run %} by {% url "passing a glob to the `--spec` flag" command-line#cypress-run-spec-lt-spec-gt %} matching the files you want to run. You should be able to pass a glob matching the specific folder where the tests are you want to run.

This feature is not available when using {% url "`cypress open`" command-line#cypress-open %} however.

## {% fa fa-angle-right %} Is there a suggested way or best practice for how I should target elements or write element selectors?

Yes. Read more about the {% url 'best practices for selecting elements here' best-practices#Selecting-Elements %}.

## {% fa fa-angle-right %} Can I prevent Cypress from failing my test when my application throws an uncaught exception error?

Yes.

By default Cypress will automatically fail tests whenever an uncaught exception bubbles up out of your app.

Cypress exposes an event for this (amongst many others) that you can listen for to either:

- Debug the error instance itself
- Prevent Cypress from failing the test

This is documented in detail on the {% url "Catalog Of Events" catalog-of-events %} page.

## {% fa fa-angle-right %} Can I override environment variables or create configuration for different environments?

Yes, you can pass configuration to Cypress via environment variables, CLI arguments, JSON files and other means.

{% url "Read the Environment Variables guide." environment-variables %}

## {% fa fa-angle-right %} Can I override or change the default user agent the browser uses?

Yes. {% url "You can override this with `userAgent` in your configuration file (`cypress.json` by default)." configuration#Browser %}

## {% fa fa-angle-right %} Can I block traffic going to specific domains? I want to block Google Analytics or other providers.

Yes. {% url "You can set this with `blacklistHosts` in your configuration file (`cypress.json` by default)." configuration#Browser %}

Also, check out our {% url 'Stubbing Google Analytics Recipe' recipes#Stubbing-and-spying %}.

## {% fa fa-angle-right %} How can I verify that calls to analytics like Google Analytics are being made correct?

You can stub their functions and then ensure they're being called.

Check out our {% url 'Stubbing Google Analytics Recipe' recipes#Stubbing-and-spying %}.

## {% fa fa-angle-right %} I'm trying to test a chat application. Can I run more than one browser at a time with Cypress?

{% url "We've answered this question in detail here." trade-offs#Multiple-browsers-open-at-the-same-time %}

## {% fa fa-angle-right %} Can I test a chrome extension? How do I load my chrome extension?

Yes. You can test your extensions by {% url 'loading them when we launch the browser.' browser-launch-api %}.

## {% fa fa-angle-right %} How can I modify or pass arguments used to launch the browser?

You use the {% url `before:browser:launch` browser-launch-api %} plugin event.

## {% fa fa-angle-right %} Can I make cy.request() poll until a condition is met?

Yes. You do it the {% url 'same way as any other recursive loop' request#Request-Polling %}.

## {% fa fa-angle-right %} Can I use the Page Object pattern?

Yes.

The page object pattern isn't actually anything "special". If you're coming from Selenium you may be accustomed to creating instances of classes, but this is completely unnecessary and irrelevant.

The "Page Object Pattern" should really be renamed to: "Using functions and creating custom commands".

If you're looking to abstract behavior or roll up a series of actions you can create reusable {% url 'Custom Commands with our API' custom-commands %}. You can also use regular ol' JavaScript functions without any of the ceremony typical with "Page Objects".

For those wanting to use page objects, we've highlighted the {% url 'best practices ' custom-commands#Best-Practices %} for replicating the page object pattern.

## {% fa fa-angle-right %} Why do my Cypress tests pass locally but not in CI?

There are many reasons why tests may fail in CI but pass locally. Some of these include:

- There is a problem isolated to the Electron browser (`cypress run` by default runs in the Electron browser)
- A test failure in CI could be highlighting a bug in your CI build process
- Variability in timing when running your application in CI (For example, network requests that resolve within the timeout locally may take longer in CI)
- Machine differences in CI versus your local machine -- CPU resources, environment variables, etc.

To troubleshoot why tests are failing in CI but passing locally, you can try these strategies:

- Test locally with Electron to identify if the issue is specific to the browser.
- You can also identify browser-specific issues by running in a different browser in CI with the `--browser` flag.
- Review your CI build process to ensure nothing is changing with your application that would result in failing tests.
- Remove time-sensitive variability in your tests. For example, ensure a network request has finished before looking for the DOM element that relies on the data from that network request. You can leverage {% url "aliasing" variables-and-aliases#Aliases %} for this.
- Ensure video recording and/or screenshots are enabled for the CI run and compare the recording to the Command Log when running the test locally.

## {% fa fa-angle-right %} Why are my video recordings freezing or dropping frames when running in CI?

Videos recorded on Continuous Integration may have frozen or dropped frames if there are not enough resources available when running the tests in your CI container. Like with any application, there needs to be the required CPU to run Cypress and record video. You can run your tests with {% url 'memory and CPU logs enabled' troubleshooting#Log-memory-and-CPU-usage %} to identify and evaluate the resource utilization within your CI.

If you are experiencing this issue, we recommend switching to a more powerful CI container or provider.

## {% fa fa-angle-right %} How can I parallelize my runs?

You can read more about parallelization {% url 'here' parallelization %}.

## {% fa fa-angle-right %} Can I run a single test or group of tests?

You can run a group of tests or a single test by placing an {% url `.only` writing-and-organizing-tests#Excluding-and-Including-Tests %} on a test suite or specific test.

You can run a single test file or group of tests by passing the `--spec` flag to {% url '`cypress run`' command-line#cypress-run %}.

## {% fa fa-angle-right %} How do I test uploading a file?

It is possible to upload files in your application but it's different based on how you've written your own upload code. You can read more about this {% issue 170 'here' %}

## {% fa fa-angle-right %} What is the projectId for?

The `projectId` is a 6 character string that helps identify your project once you've {% url "set up your tests to record" runs %}. It's generated by Cypress and typically is found in your {% url "configuration file (`cypress.json` by default)" configuration %} file.

```json
{
  "projectId": "a7bq2k"
}
```

For further detail see the {% url Identification projects#Identification %} section of the {% url "Dashboard Service" dashboard-introduction%} docs.

## {% fa fa-angle-right %} What is a Record Key?

A *Record Key* is a GUID that's generated automatically by Cypress once you've {% url "set up your tests to record" runs %}. It helps identify your project and authenticate that your project is even *allowed* to record tests.

You can find your project's record key inside of the *Settings* tab in the Test Runner.

{% imgTag /img/dashboard/record-key-shown-in-desktop-gui-configuration.jpg "Record Key in Configuration Tab" %}

For further detail see the {% url Identification projects#Identification %} section of the {% url "Dashboard Service" dashboard-introduction%} docs.

## {% fa fa-angle-right %} How do I check that an email was sent out?

{% note warning 'Anti-Pattern' %}
Don't try to use your UI to check email. Instead opt to programmatically use 3rd party APIs or talk directly to your server. Read about this {% url 'best practice' best-practices#Visiting-external-sites %} here.
{% endnote %}

## {% fa fa-angle-right %} How do I wait for multiple XHR requests to the same url?

You should set up an alias (using {% url `.as()` as %}) to a single {% url `cy.route()` route %} that matches all of the XHRs. You can then {% url `cy.wait()` wait %} on it multiple times. Cypress keeps track of how many matching XHR requests there are.

```javascript
cy.server()
cy.route('users').as('getUsers')
cy.wait('@getUsers')  // Wait for first GET to /users/
cy.get('#list>li').should('have.length', 10)
cy.get('#load-more-btn').click()
cy.wait('@getUsers')  // Wait for second GET to /users/
cy.get('#list>li').should('have.length', 20)
```

## {% fa fa-angle-right %} How do I seed / reset my database?

You can use {% url `cy.request()` request %}, {% url `cy.exec()` exec %}, or {% url `cy.task()` task %} to talk to your back end to seed data.

You could also stub XHR requests directly using {% url `cy.route()` route %} which avoids ever even needing to fuss with your database.

## {% fa fa-angle-right %} How do I test elements inside an iframe?

As of {% issue 136#issuecomment-328100955 `0.20.0` %} you can now wrap the elements of an iframe and work with them.

We have an {% issue 685 'open proposal' %} to expand the APIs to support "switching into" an iframe and then back out of them.

## {% fa fa-angle-right %} How do I preserve cookies / localStorage in between my tests?

By default, Cypress automatically {% url "clears all cookies **before** each test" clearcookies %} to prevent state from building up.

You can preserve specific cookies across tests using the {% url "Cypress.Cookies api" cookies %}:

```javascript
// now any cookie with the name 'session_id' will
// not be cleared before each test runs
Cypress.Cookies.defaults({
  whitelist: 'session_id'
})
```

You **cannot** currently preserve localStorage across tests and can read more {% issue '461#issuecomment-325402086' 'here' %}.

## {% fa fa-angle-right %} Some of my elements animate in; how do I work around that?

Oftentimes you can usually account for animation by asserting {% url "`.should('be.visible')`" should %} or {% url "another assertion" introduction-to-cypress#Assertions %} on one of the elements you expect to be animated in.

```javascript
// assuming a click event causes the animation
cy.get('.element').click().should('not.have.class', 'animating')
```

If the animation is especially long, you could extend the time Cypress waits for the assertion to pass by increasing the `timeout` of the previous command before the assertion.

```javascript
cy.get('button', { timeout: 10000 }) // wait up to 10 seconds for this 'button' to exist
  .should('be.visible')              // and to be visible

cy.get('.element').click({ timeout: 10000 }).should('not.have.class', 'animating')
// wait up to 10 seconds for the .element to not have 'animating' class
```

However, most of the time you don't even have to worry about animations. Why not?  Cypress will {% url "automatically wait" interacting-with-elements %} for elements to stop animating prior to interacting with them via action commands like `.click()` or `.type()`.

## {% fa fa-angle-right %} Can I test anchor links that open in a new tab?

Cypress does not and may never have multi-tab support for various reasons.

Luckily there are lots of clear and safe workarounds that enable you to test this behavior in your application.

{% url 'Read through the recipe on tab handling and links to see how to test anchor links.' recipes#Testing-the-DOM %}

## {% fa fa-angle-right %} Can I dynamically test multiple viewports?

Yes, you can. We provide an {% url 'example here' viewport#Width-Height %}.

## {% fa fa-angle-right %} Can I run the same tests on multiple subdomains?

Yes. In this example, we loop through an array of urls and make assertions on the logo.

```javascript
const urls = ['https://docs.cypress.io', 'https://www.cypress.io']

describe('Logo', () => {
  urls.forEach((url) => {
    it(`Should display logo on ${url}`, () => {
      cy.visit(url)
      cy.get('#logo img')
        .should('have.attr', 'src')
        .and('include', 'logo')
    })
  })
})
```

{% imgTag /img/faq/questions/command-log-of-dynamic-url-test.png "Command Log multiple urls" %}

## {% fa fa-angle-right %} How do I require or import node modules in Cypress?

The code you write in Cypress is executed in the browser, so you can import or require JS modules, *but* only those that work in a browser.

You can `require` or `import` them as you're accustomed to. We preprocess your spec files with `babel` and `browserify`.

We recommend utilizing one of the following to execute code outside of the browser. Furthermore, you can use your own Node version during code excecution by setting the {% url "`nodeVersion`" configuration#Node-version %} in your configuration.

- {% url `cy.task()` task %} to run code in Node via the {% url "`pluginsFile`" configuration#Folders-Files %}
- {% url `cy.exec()` exec %} to execute a shell command

{% url 'Check out the "Node Modules" example recipe.' recipes#Fundamentals %}

## {% fa fa-angle-right %} Is there a way to give a proper SSL certificate to your proxy so the page doesn't show up as "not secure"?

No, Cypress modifies network traffic in real time and therefore must sit between your server and the browser. There is no other way for us to achieve that.

## {% fa fa-angle-right %} Is there any way to detect if my app is running under Cypress?

You can check for the existence of `window.Cypress`, in your **application code**.

Here's an example:

```javascript
if (window.Cypress) {
  // we are running in Cypress
  // so do something different here
  window.env = 'test'
} else {
  // we are running in a regular ol' browser
}
```

## {% fa fa-angle-right %} Do you allow before, beforeEach, after, or afterEach hooks?

Yes. You can read more {% url "here" writing-and-organizing-tests#Hooks %}.

## {% fa fa-angle-right %} I tried to install Cypress in my CI, but I get the error: `EACCES: permission denied`.

First, make sure you have {% url "Node" https://nodejs.org %} installed on your system. `npm` is a Node package that is installed globally by default when you install Node and is required to install our {% url "`cypress` npm  package" command-line %}.

Next, you'd want to check that you have the proper permissions for installing on your system or you may need to run `sudo npm install cypress`.

## {% fa fa-angle-right %} Is there a way to test that a file got downloaded? I want to test that a button click triggers a download.

There are a lot of ways to test this, so it depends. You'll need to be aware of what actually causes the download, then think of a way to test that mechanism.

If your server sends specific disposition headers which cause a browser to prompt for download, you can figure out what URL this request is made to, and use {% url "cy.request()" request %} to hit that directly. Then you can test that the server send the right response headers.

If it's an anchor that initiates the download, you could test that it has the right `href` property. As long as you can verify that clicking the button is going to make the right HTTP request, there's nothing else to test for.

In the end, it's up to you to know your implementation and to test enough to cover everything.

## {% fa fa-angle-right %} Is it possible to catch the promise chain in Cypress?

No. You cannot add a `.catch` error handler to a failed command. {% url "Read more about how the Cypress commands are not Promises" introduction-to-cypress#Commands-Are-Not-Promises %}

## {% fa fa-angle-right %} Is there a way to modify the screenshots/video resolution?

Not at the moment. {% issue 587 "There is an open issue for this." %}

## {% fa fa-angle-right %} Does Cypress support ES7?

Yes. You can customize how specs are processed by using one of our {% url 'preprocessor plugins' plugins %} or by {% url 'writing your own custom preprocessor' preprocessors-api %}.

Typically you'd reuse your existing `babel` and `webpack` configurations.

## {% fa fa-angle-right %} How does one determine what the latest version of Cypress is?

There are a few ways.

- The easiest way is probably to check our {% url "changelog" changelog %}.
- You can also check the latest version {% url "here" https://download.cypress.io/desktop.json %}.
- It's also always in our {% url "repo" https://github.com/cypress-io/cypress %}.

## {% fa fa-angle-right %} Is there an ESLint plugin for Cypress or a list of globals?

Yes! Check out our {% url "ESLint plugin" https://github.com/cypress-io/eslint-plugin-cypress %}. It will set up all the globals you need for running Cypress, including browser globals and {% url "Mocha" https://mochajs.org/ %} globals.

## {% fa fa-angle-right %} When I visit my site directly, the certificate is verified, however the browser launched through Cypress is showing it as "Not Secure". Why?

This is normal. Cypress modifies the traffic between your server and the browser. The browser notices this and displays a certificate warning. However, this is purely cosmetic and does not alter the way your application under test runs in any way, so you can safely ignore this warning.

## {% fa fa-angle-right %} Is there an option to run Cypress in CI with Developer Tools open? We want to track network and console issues.

No. This is definitely the motivation behind {% issue 448 "this open issue" %}, but there is not a way to run Cypress in `cypress run` with Developer Tools open.

You may try running the tests locally and {% url "select the Electron browser" launching-browsers#Electron-Browser %}, that is as close as you will get with Developer Tools open and replicating the environment that was run during `cypress run`.

## {% fa fa-angle-right %} How do I run the server and tests together and then shutdown the server?

To start the server, run the tests and then shutdown the server we recommend {% url "these npm tools" continuous-integration#Boot-your-server %}.

## {% fa fa-angle-right %} Can I test my Electron app?

Testing your Electron app will not 'just work', as Cypress is designed to test anything that runs in a browser and Electron is a browser + Node.

That being said, we use Cypress to test our own Desktop app's front end - by stubbing events from Electron. These tests are open source so you can check them out {% url "here" https://github.com/cypress-io/cypress/tree/develop/packages/desktop-gui/cypress/integration %}.

## {% fa fa-angle-right %} I found a bug! What do I do?

- Search existing {% url "open issues" https://github.com/cypress-io/cypress/issues %}, it may already be reported!
- Update Cypress. Your issue may have {% url "already been fixed" changelog %}.
- {% open_an_issue %}. Your best chance of getting a bug looked at quickly is to provide a repository with a reproducible bug that can be cloned and run.

## {% fa fa-angle-right %} What are your best practices for organizing tests?

We see organizations _starting_ with Cypress by placing end-to-end tests in a separate repo. This is a great practice that allows someone on the team to prototype a few tests and evaluate Cypress within minutes. As the time passes and the number of tests grows, we _strongly suggest_ moving end-to-end tests to live right alongside your front end code. This brings many benefits:

- engages developers in writing end-to-end tests sooner
- keeps tests and the features they test in sync
- tests can be run every time the code changes
- allows code sharing between the application code and the tests (like selectors)

## {% fa fa-angle-right %} What is the right balance between custom commands and utility functions?

There is already a great section in {% url "Custom Commands" custom-commands#Best-Practices %} guide that talks about trade-offs between custom commands and utility functions. We feel reusable functions in general are a way to go. Plus they do not confuse {% issue 1065 'IntelliSense like custom commands do' %}.

## {% fa fa-angle-right %} Can I print the list of commands from a test in the terminal?

If a test fails, Cypress takes a screenshot image, but does not print the list of commands in the terminal, only the failed assertion. There is a user space plugin {% url cypress-failed-log https://github.com/bahmutov/cypress-failed-log %} that saves a JSON file with all commands from a failed test. We are also working on mirroring `console.log` messages from the browser in the terminal, see {% issue 2078 %}.

## {% fa fa-angle-right %} Can my tests interact with Redux / Vuex data store?

Usually your end-to-end tests interact with the application through public browser APIs: DOM, network, storage, etc. But sometimes you might want to make assertions against the data held inside the application's data store. Cypress helps you do this. Tests run right in the same browser instance and can reach into the application's context using {% url `cy.window` window %}. By conditionally exposing the application reference and data store from the application's code, you can allow the tests to make assertions about the data store, and even drive the application via Redux actions.

- see {% url "Testing Redux Store" https://www.cypress.io/blog/2018/11/14/testing-redux-store/ %} blog post and {% url "Redux Testing" recipes#Blogs %} recipe.
- see {% url "Testing Vue web applications with Vuex data store & REST back end" https://www.cypress.io/blog/2017/11/28/testing-vue-web-application-with-vuex-data-store-and-rest-backend/ %} blog post and {% url 'Vue + Vuex + REST Testing' recipes#Blogs %} recipe.

## {% fa fa-angle-right %} How do I spy on console.log?

To spy on ```console.log``` you should use {% url "`cy.stub()`" stub %}.

```javascript
cy.visit('/', {
  onBeforeLoad(win) {
    cy.stub(win.console, 'log').as('consoleLog')
  }
})

//...
cy.get('@consoleLog').should('be.calledWith', 'Hello World!')
```

Also, check out our {% url 'Stubbing `console` Receipe' recipes#Stubbing-and-spying %}.

## {% fa fa-angle-right %} How do I use special characters with `cy.get()`?

Special characters like `/`, `.` are valid characters for ids {% url "according to the CSS spec" https://www.w3.org/TR/html50/dom.html#the-id-attribute %}. 

To test elements with those characters in ids, they need to be escaped with {% url "`CSS.escape`" https://developer.mozilla.org/en-US/docs/Web/API/CSS/escape %} or {% url "`Cypress.$.escapeSelector`" https://api.jquery.com/jQuery.escapeSelector/ %}.

```html
<!doctype html>
<html lang="en">
<body>
  <div id="Configuration/Setup/TextField.id">Hello World</div>
</body>
</html>
```

```js
it('test', () => {
  cy.visit('index.html')
  cy.get(`#${CSS.escape('Configuration/Setup/TextField.id')}`)
    .contains('Hello World')

  cy.get(`#${Cypress.$.escapeSelector('Configuration/Setup/TextField.id')}`)
    .contains('Hello World')
})
```

Note that `cy.$$.escapeSelector()` doesn't work. `cy.$$` doesn't refer to `jQuery`. It only queries DOM. {% url "Learn more about why" $#Notes %}
