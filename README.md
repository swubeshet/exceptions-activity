# Bank Accounts

We will be working with the concept of bank accounts in order to explore more object-oriented code as well as a few other new topics.

## Baseline Setup

1. This is an individual, stage 1 project.
1. Fork the project master.
1. Clone the forked repo: `$ git clone [YOUR FORKED REPO URL]`
1. `cd` into the dir create:d `$ cd BankAccounts`
1. Run `git remote -v` to verify the folder you are in corresponds to the fork you have created.
1. Run `gem install minitest-skip` to install an extra gem for testing (more on what this actually does later).


## Wave 1

### Learning Goals
- Create a **class** inside of a **module**
- Create **methods** inside the **class** to perform actions
- Learn how Ruby does error handling
- Verify code correctness by **testing**

### Requirements

Create a `Bank` module which will contain your `Account` class and any future bank account logic.

Create an `Account` class which should have the following functionality:
- A new account should be created with an ID and an initial balance
- Should have a `withdraw` method that accepts a single parameter which represents the amount of money that will be withdrawn. This method should return the updated account balance.
- Should have a `deposit` method that accepts a single parameter which represents the amount of money that will be deposited. This method should return the updated account balance.
- Should be able to access the current `balance` of an account at any time.

#### Error handling

- A new account cannot be created with initial negative balance - this will `raise` an `ArgumentError` (Google this)
- The `withdraw` method does not allow the account to go negative. Instead it will output a warning message and return the original un-modified balance.

#### Testing

To ensure the what we've built is correct, we'll write tests alongside the code. To get started we've provided you with a basic `assertions.rb` in the `test` directory, which should look very familiar. Feel free to edit this file and add more assertions as you see fit.

To write tests, add a new file `test/account_test.rb`. At the top, use `require_relative` to access the assertions and your account code:

```ruby
require_relative 'assertions'
require_relative '../account'
```

Run the tests from the project root with

```bash
$ ruby test/account_test.rb
```

At a minimum, you should have one test for each of the behavior requirements above, as well as one for each error case (that's 6 tests minimum). Each test you write should have the following form:

1. Use `puts` to write a descriptive name to the console
1. Create a new `Account` object
1. Do things to the `Account`, and assert that the results are correct

### Optional:
Make sure to write tests for any optionals you implement!

- Create an `Owner` class which will store information about those who own the `Accounts`.
  - This should have info like name and address and any other identifying information that an account owner would have.
- Add an `owner` property to each Account to track information about who owns the account.
  - The `Account` can be created with an `owner`, OR you can create a method that will add the `owner` after the `Account` has already been created.

## Wave 2

### Learning Goals
- Create and use class methods
- Use a CSV file for loading data

### Requirements
- Update the `Account` class to be able to handle all of these fields from the CSV file used as input.
  - For example, manually choose the data from the first line of the CSV file and ensure you can create a new instance of your Account using that data
- Add the following **class** methods to your existing `Account` class
  - `self.all` - returns a collection of `Account` instances, representing all of the Accounts described in the CSV. See below for the CSV file specifications.
  - `self.find(id)` - returns an instance of `Account` where the value of the id field in the CSV matches the passed parameter.
    - **Question:** what should your program do if `Account.find` is called with an ID that doesn't exist?

#### CSV Data File

The data, in order in the CSV, consists of:  

| Field    | Type     | Description
|----------|----------|------------
| ID       | Fixnum   | A unique identifier for that Account  
| Balance  | Fixnum   | The account balance amount, in cents (i.e., 150 would be $1.50)  
| OpenDate | Datetime | When the account was opened

#### Testing:

Since it doesn't take any parameters, `Account.all` only needs one test. `Account.find` will need one for when the ID exists, and one for when it does not.

### Optional:
First, implement the optional requirement from Wave 1

Then, add the following **class** methods to your existing `Owner` class
  - `self.all` - returns a collection of `Owner` instances, representing all of the Owners described in the CSV. See below for the CSV file specifications
  - `self.find(id)` - returns an instance of `Owner` where the value of the id field in the CSV matches the passed parameter

#### CSV Data File
The data, in order in the CSV, consists of:

| Field          | Type   | Description
|----------------|--------|------------
| ID             | Fixnum | A unique identifier for that Owner
| Last Name      | String | The owner's last name   
| First Name     | String | The owner's first name  
| Street Address | String | The owner's street address  
| City           | String | The owner's city  
| State          | String | The owner's state  

To create the relationship between the accounts and the owners use the `account_owners` CSV file. The data for this file, in order in the CSV, consists of:

| Field      | Type   | Description
|------------|--------|------------
| Account ID | Fixnum | A unique identifier corresponding to an Account
| Owner ID   | Fixnum | A unique identifier corresponding to an Owner

This type of table, where records from other tables are assoicated with each other, is often called a _join table_. We'll talk about them as a class in a few weeks.

## Wave 3
### Learning Goals
- Use inheritance to share some behavior across classes
- Enhance functionality built in Wave 1

### Requirements

For wave 3, you will create two new classes: `SavingsAccount` and `CheckingAccount`. Both should inherit behavior from the `Account` class.

#### SavingsAccount
Create a `SavingsAccount` class which should inherit behavior from the `Account` class. It should include the following updated functionality:
- The initial balance cannot be less than $10. If it is, this will `raise` an `ArgumentError`
- Updated withdrawal functionality:
  - Each withdrawal 'transaction' incurs a fee of $2 that is taken out of the balance.
  - Does not allow the account to go below the $10 minimum balance - Will output a warning message and return the original un-modified balance

It should include the following new method:
- `#add_interest(rate)`: Calculate the interest on the balance and add the interest to the balance. Return the **interest** that was calculated and added to the balance (not the updated balance).
  - Input rate is assumed to be a percentage (i.e. 0.25).
  - The formula for calculating interest is `balance * rate/100`
    - Example: If the interest rate is 0.25 and the balance is $10,000, then the interest that is returned is $25 and the new balance becomes $10,025.

#### CheckingAccount
Create a `CheckingAccount` class which should inherit behavior from the `Account` class. It should include the following updated functionality:
- Updated withdrawal functionality:
  - Each withdrawal 'transaction' incurs a fee of $1 that is taken out of the balance. Returns the updated account balance.
    - Does not allow the account to go negative. Will output a warning message and return the original un-modified balance.
- `#withdraw_using_check(amount)`: The input amount gets taken out of the account as a result of a check withdrawal. Returns the updated account balance.
  - Allows the account to go into overdraft up to -$10 but not any lower
  - The user is allowed three free check uses in one month, but any subsequent use adds a $2 transaction fee
- `#reset_checks`: Resets the number of checks used to zero


### Optional:

Create a `MoneyMarketAccount` class which should inherit behavior from the `Account` class.
- A maximum of 6 transactions (deposits or withdrawals) are allowed per month on this account type
- The initial balance cannot be less than $10,000 - this will `raise` an `ArgumentError`
- Updated withdrawal logic:
  - If a withdrawal causes the balance to go below $10,000, a fee of $100 is imposed and no more transactions are allowed until the balance is increased using a deposit transaction.
  - Each transaction will be counted against the maximum number of transactions
- Updated deposit logic:
  - Each transaction will be counted against the maximum number of transactions
  - Exception to the above: A deposit performed to reach or exceed the minimum balance of $10,000 is not counted as part of the 6 transactions.
- `#add_interest(rate)`: Calculate the interest on the balance and add the interest to the balance. Return the interest that was calculated and added to the balance (not the updated balance).
    - Note** This is the same as the `SavingsAccount` interest.
- `#reset_transactions`: Resets the number of transactions to zero
