# Essent Typescript Bootcamp: Final Assignment

## Introduction

In order to get a view on how far we've come during the bootcamp sessions, we'd like the participants to do one final assignment.

- The assignment should be completed individually, without collaboration or copying from others.
- This assignment can be done at home.
- You may take as long as you'd like until the deadline to complete this assignment.
- If you can’t complete everything or all parts, try to complete as much as possible.
- Use `npx create-nx-workspace@latest` to scaffold your project. Choose `node` and `express` when asked. Your server must run on port `3000`. Make sure you're running node `v20` on your machine.
- To review the assignment, automated tests are run on the endpoints. Therefore it's important that the provided API contracts are implemented exactly how they are subscribed.
- When you're tasked to save data, do so in memory. No real database connection or something else is needed for this.

## Evaluation criteria

Your result will be evaluated based on 1) correctness and functionality, 2) good coding practices and 3) clean code and maintainability.

### Correctness and functionality

- The endpoints should return results as specified in the assignment. We will test this with automated tests.

### Good coding practices

- Repository creation: Create a (public) GitHub repository from scratch for the assignment.
- Commit messages: Use commit messages that satisfy the rules stated by the [conventional commits standard](https://www.conventionalcommits.org/en/v1.0.0/). Make sure that the commit messages accurately describe the changes made. Commit code regularly, at least once per assignment (1-9), but more frequent commits are encouraged.
- Typing: Define and use types and interfaces (preferred over classes for this assignment) where needed to ensure code clarity and maintainability.
- Testing: Test your logic and endpoints thoroughly with unit tests. The homework of the last session (Fullstack Open Part 4b) explained how to unit test endpoints.

### Clean code and maintainability

- Code layout and styling: Ensure proper code layout and indentation. Follow the AirBnB code style guide and resolve all linting warnings and errors.
- Linting: Apply linting rules to maintain code quality and consistency. Use the AirBnB code style guide or any other agreed-upon style guide. (Note that you need to configure AirBnB linting as it is not in the Nx node repo by default.)
- Variable and function names: Use meaningful and descriptive names for variables and functions. Ensure that the names accurately represent their purpose and functionality.
- Bonus considerations: Consider other aspects such as code reusability (e.g., through functions, adhering to the DRY principle) and error handling.

## Case: Essent Savings Program

In addition to electricity and gas, Essent also offers “Energy Products”. These are mainly products and services to make homes more sustainable, such as solar panels, heat pumps and insulation. Because these are often major expenses, Essent wants to offer its customers a savings plan. It is possible to open a savings account to which money can be deposited at any time. Once in the savings account, money can not be withdrawn, but it can be used to purchase Essent energy products.

In this assignment you will build the backend of this system. The implementation of this backend consists of 4 parts. Each part depends on the implementation of the former part.

### Part A: Accounts

In the first part we're creating endpoints that will handle the registration and presentation of user accounts. Implement these API's according to the following contract:

#### 1) POST: /accounts

This endpoint should create a new account. The id of this account should be a randomly generated uuidv4. Use the [uuid](https://www.npmjs.com/package/uuid) package to do this. The balance returned should always be a number. In part 1, this balance will always be 0.

| Endpoint  | Method | Case          | Request Body       | Response Body                                                              | Response Status Code |
| --------- | ------ | ------------- | ------------------ | -------------------------------------------------------------------------- | -------------------- |
| /accounts | POST   | Success       | `{ name: 'Jaap' }` | `{ id: '8cb5c25c-fd5b-4299-9f21-0cf9530f2187', name: 'Jaap', balance: 0 }` | 200                  |
| /accounts | POST   | Invalid Input |                    | _None_                                                                     | 400                  |

#### 2) GET: /accounts

This endpoint should retreive all accounts currently saved in the system. The balance returned should always be a number. In part 1, this balance will always be 0.

| Endpoint  | Method | Case    | Request Body | Response Body                                                                | Response Status code |
| --------- | ------ | ------- | ------------ | ---------------------------------------------------------------------------- | -------------------- |
| /accounts | GET    | Success | _None_       | `[{ id: '8cb5c25c-fd5b-4299-9f21-0cf9530f2187'; name: 'Jaap'; balance: 0 }]` | 201                  |

#### 3) GET: /accounts/:accountId

This endpoint should retreive the account corresponding the given `accountId`. The balance returned should always be a number. In part 1, this balance will always be 0.

| Endpoint             | Method | Case      | Request Body | Response Body                                                              | Response Status code |
| -------------------- | ------ | --------- | ------------ | -------------------------------------------------------------------------- | -------------------- |
| /accounts/:accountId | GET    | Success   | _None_       | `{ id: '8cb5c25c-fd5b-4299-9f21-0cf9530f2187'; name: 'Jaap'; balance: 0 }` | 200                  |
| /accounts/:accountId | GET    | Not Found | _None_       | _None_                                                                     | 404                  |

### Part B: Purchasing Products

In the second part, we're going to implement API's to allow for deposits and the purchasing of products.

For these APIs we're introducing the request header `Simulated-Day`. This header represents the amount of days that are passed after the start of the server. This header is needed to simulate the passing of time. The header will look like this: `Simulated-Day: 30`, meaning 30 days after the start of the server. You can assume accounts are always created at `Simulated-Day = 0`.

When the server starts (`Simulated-Day = 0`), the following products exist in the catalogue of Essent:

```typescript
export const products = [
  {
    id: "solar",
    title: "Solar Panel",
    description: "Super duper Essent solar panel",
    stock: 10,
    price: 750,
  },
  {
    id: "insulation",
    title: "Insulation",
    description: "Cavity wall insulation",
    stock: 10,
    price: 2500,
  },
  {
    id: "heatpump",
    title: "Awesome Heatpump",
    description: "Hybrid heat pump",
    stock: 3,
    price: 5000,
  },
];
```

Please implement the following endpoints:

#### 4) POST: /accounts/:accountId/deposits

This endpoint should register the deposit. The `accountId` should be the id of the account on which to deposited the money. The id returned should be the deposit id. Deposits should only count towards your balance at the end of the day (meaning, if you're deposting an mount on day `3`, this deposit should only be reflected in your balance on day `4` and onwards).

##### Request

| Endpoint                      | Method | Request Body      | Request Header      |
| ----------------------------- | ------ | ----------------- | ------------------- |
| /accounts/:accountId/deposits | POST   | `{ amount: 250 }` | `Simulated-Day: 10` |

##### Response

| Case          | Response Body                                                                | Response Status code |
| ------------- | ---------------------------------------------------------------------------- | -------------------- |
| Success       | `{ id: '8cb5c25c-fd5b-4299-9f21-0cf9530f2187', name: 'Jaap', balance: 250 }` | 201                  |
| Invalid input | _None_                                                                       | 400                  |

#### 5) POST: /accounts/:accountId/purchases

This endpoint should register a product purchase. An account may only purchase this product if it has enough funds and if the product is in stock. A purchase is illegal if the simulated day of the new purchase is earlier than the simulated day of the latest purchase. This means you can only register purchases chronologically.

##### Request

| Endpoint                       | Method | Request Body                | Request Header      |
| ------------------------------ | ------ | --------------------------- | ------------------- |
| /accounts/:accountId/purchases | POST   | `{ productId: 'heatpump' }` | `Simulated-Day: 11` |

##### Response

| Case                  | Response Body | Response Status code |
| --------------------- | ------------- | -------------------- |
| Success               | _None_        | 201                  |
| Not enough stock      | _None_        | 409                  |
| Not enough funds      | _None_        | 409                  |
| Invalid input         | _None_        | 400                  |
| Simulated day illegal | _None_        | 400                  |

#### 6) POST: /products

This endpoint should add a product. This product should be considered available from day 0. The response should be the added product. The id of the product should be randomly generated.

##### Request

| Endpoint  | Method | Request Body                                                                            | Request Header |
| --------- | ------ | --------------------------------------------------------------------------------------- | -------------- |
| /products | POST   | `{title: "Solar System", description: "Some nice description", price: 1000, stock: 5 }` | _None_         |

##### Response

| Case          | Response Body                                                                                                                 | Response Status code |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------- | -------------------- |
| Success       | `{id: "1231-gagawg-2415g-agagw-355325", title: "Solar System", description: "Some nice description", price: 1000, stock: 5 }` | 201                  |
| Invalid input | _None_                                                                                                                        | 400                  |

#### 7) GET: /products

This endpoint should fetch the list of products. The products should reflect the current stock based on the point in time given by the `Simulated-Day` header.

##### Request

| Endpoint  | Method | Request Body | Request Header      |
| --------- | ------ | ------------ | ------------------- |
| /products | GET    | _None_       | `Simulated-Day: 12` |

##### Response

| Case    | Response Body                                                                                                                                                                                        | Response Status code |
| ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------- |
| Success | List of products as defined above, including inventories, so: `[{    id: 'heatpump',    title: 'Awesome Heatpump',    description: 'Hybrid heat pump',    stock: 3,    price: 5000 }, {...}, {...}]` | 200                  |

#### 8) GET: /products/:productId

This endpoint should fetch a particular product. This product should reflect the current stock based on the point in time given by the `Simulated-Day` header.

##### Request

| Endpoint             | Method | Request Body | Request Header      |
| -------------------- | ------ | ------------ | ------------------- |
| /products/:productId | GET    | _None_       | `Simulated-Day: 14` |

##### Response

| Case      | Response Body                                                                                                          | Response Status code |
| --------- | ---------------------------------------------------------------------------------------------------------------------- | -------------------- |
| Success   | `{    id: 'heatpump',    title: 'Awesome Heatpump',    description: 'Hybrid heat pump',    stock: 3,    price: 5000 }` | 200                  |
| Not found | _None_                                                                                                                 | 404                  |

### Part C: Interest

You might have wondered about the added value of the savings account compared to just buying Energy Products directly. The answer lies in a relatively high interest rate of 8%. Implement the interest endpoint. Next, extend the GET endpoints of parts A and B to take into account this interest rate. Start by supporting the `Simulated-Day` header and use the following rules to implement the interest mechanism:

1. Interest is paid out every 30th day after all other transactions of that day are processed
2. The interest is 8% on a yearly basis which comes down to 30/365 \* 8% over the average balance of each 30 day period.
3. The balance of the account should reflect the gained interest over the period of time (which is indicated by the header).
4. The validity of the purchase of a product should take the savings from interest into account.

### Bonus part

#### 9) POST: /interest

This endpoint should set the interest rate. The interest rates are calculated dynamically based on this rate. Mind that this rate change only activates on the day reflected by the `Simulated-Day` header. All interest before this date should reflect the previous interest rate.

##### Request

| Endpoint  | Method | Request Body     | Example Request Body               |
| --------- | ------ | ---------------- | ---------------------------------- |
| /interest | POST   | `{ rate: 0.07 }` | `{ rate: 0.07 }` => 7% yearly rate |

##### Response

| Case          | Response Body | Response Status code |
| ------------- | ------------- | -------------------- |
| Success       | _None_        | 200                  |
| Invalid input | _None_        | 400                  |

## Turning in the assignment

Once finished, share the URL of your repository with Wesley and Thijs. You have one week for the assignment, so you have up to and including the 13th of November to submit the assignment.
