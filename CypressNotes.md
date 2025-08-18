# Cypress Interview Questions & Answers

## What is the default command timeout in Cypress?
**Answer:** 4 seconds (can be changed using `defaultCommandTimeout` in `cypress.config.js`).

---

## How do you handle assertions in Cypress?
Using **Chai assertion library** (built-in):

```js
cy.get('h1').should('contain', 'Welcome');
```

---

## How do you run Cypress tests in headless mode?
```bash
npx cypress run --headless
```

---

## How do you handle iframes in Cypress?
Cypress doesn’t support iframes natively; we use a plugin like `cypress-iframe`.

```js
cy.frameLoaded('#iframeId');
cy.iframe().find('button').click();
```

---

## How do you handle multiple tabs/windows in Cypress?
Cypress doesn’t support multiple browser tabs. Instead, we stub or force links to open in the same tab:

```js
cy.get('a').invoke('removeAttr', 'target').click();
```

---

# Advanced Cypress Questions

## How do you mock API responses in Cypress?
```js
cy.intercept('GET', '/api/users', { fixture: 'users.json' }).as('getUsers');
cy.visit('/users');
cy.wait('@getUsers');
```

---

## How do you handle environment variables in Cypress?
Defined in `cypress.config.js` or via CLI:  

```bash
npx cypress run --env username=admin,password=1234
```

```js
Cypress.env('username');
```

---

## What are Cypress fixtures?
External JSON or data files stored in the `fixtures/` folder, used to mock data.

```js
cy.fixture('user.json').then((user) => {
  cy.get('#username').type(user.name);
});
```

---

## How do you retry failed tests in Cypress?
Cypress automatically retries commands until they pass or timeout.

For test retries, add in config:  

```js
retries: { runMode: 2, openMode: 1 }
```

---

## What are Cypress custom commands?
User-defined reusable commands stored in `cypress/support/commands.js`.

```js
Cypress.Commands.add('login', (username, password) => {
  cy.get('#user').type(username);
  cy.get('#pass').type(password);
  cy.get('#login').click();
});
```
