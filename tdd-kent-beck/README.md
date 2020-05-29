# Test Driven Development - Kent Beck

## Table of Contents

- Money Example


## Money Example

### Objectives

- Multi Currency Support for existing Dollar based Payment System

### Workflow

- Quickly add a test
- Run all tests  and see the new one  fail
- Make a Little change
- Run all tests and see them all succeed
- Refactor to remove duplication
 
 
Running the tests we are making sure what is obvious to us is also obvious to our system. 
 
As soon as we get an error

- we back up, 
- shift to fake implementations 
- and refactor to right code
 

The translation of feeling into test. The longer we do it the better we able to translate our aesthetic judgement into tests

### Problem

- Report Format

![](assets/report-format.png)
- to make a multi currency report we need to add currencies
![](assets/add-currencies.png)
- also specify exchange rates
![](assets/exchange-reports.png)

```
$5 + 10CHF = $10 if rate is 2:1
$5 * 2 = $10
```

```python
# $5 * 2 = $10
def test_multiply_dollars():
    dollar = Dollar(5)
    dollar.times(2)
    assert dollar.amount == 10
```

^ Problems?

- no Dollar Class
- no times method
- no amount attribute
- integer for Currency

```
Traceback (most recent call last):
  File "test.py", line 6, in <module>
    test_multiply_dollars()
  File "test.py", line 2, in test_multiply_dollars
    dollar = Dollar(5)
NameError: global name 'Dollar' is not defined
```

Let's solve one by one

- first lets create a Dollar Class

```python
class Dollar:
    pass
```

- then another error pops up

```
Traceback (most recent call last):
  File "test.py", line 9, in <module>
    test_multiply_dollars()
  File "test.py", line 5, in test_multiply_dollars
    dollar = Dollar(5)
TypeError: this constructor takes no arguments
```

- lets create constructor with arguments

```python
class Dollar:
    def __init__(self, amount):
        pass
```

- next error about times method

```
Traceback (most recent call last):
  File "test.py", line 9, in <module>
    test_multiply_dollars()
  File "test.py", line 6, in test_multiply_dollars
    dollar.times(2)
AttributeError: Dollar instance has no attribute 'times'

```

- added `times` method

```python
class Dollar:
    def __init__(self, amount):
        pass

    def times(self, multiplier):
        pass
```

- next error - no amount field

```
Traceback (most recent call last):
  File "test.py", line 9, in <module>
    test_multiply_dollars()
  File "test.py", line 7, in test_multiply_dollars
    assert dollar.amount == 10
AttributeError: Dollar instance has no attribute 'amount'
```

- adding amount field

```python
class Dollar:
    def __init__(self, amount):
        self.amount = None

    def times(self, multiplier):
        pass
```

- then we are getting assertion error
```
Traceback (most recent call last):
  File "test.py", line 9, in <module>
    test_multiply_dollars()
  File "test.py", line 7, in test_multiply_dollars
    assert dollar.amount == 10
AssertionError
```
- fixing the logic just enough to pass the test

```python
class Dollar:
    def __init__(self, amount):
        self.amount = None

    def times(self, multiplier):
        self.amount = 5 * 2
```

- and in the end it passed

- now eliminating duplication

> Depenedecy is a key problem, the symptom is duplication

^ Objects are excellent for abstracting away the duplication of logic.

By eliminating dupliation before we go on to the next test, we maximize our chance of being able to get the next test running with one and only one change

```python
class Dollar:
    def __init__(self, amount):
        self.amount = amount

    def times(self, multiplier):
        self.amount = self.amount * multiplier
```

![](assets/9f184447.png)

Our current workflow

- made a todo list
- snippet explaining our course of action input/output
- made the test compile with stubs
- made the test run by dummy code (horrible sins)
- gradually generalizing the working code replacing consts with vars
- rather than addressing all, tackling them one by one

---

*TDD Cycle*

- Clearly Delineate the interface in paper
- Make it run with dummy code. Red --> Green Feedback Loop
  - if the solution is obvious do it once
  - otherwise take small step and go to that phase incrementally
- Now Refactor the code and remove the duplicated stuff away

Divide and Conquer. 
First make it work, then refactor to achieve clean code

![](assets/e631dfc9.png)

Let's focus on Dollar Side Effects - we want to call the method multiple times without changing the initial `amount` field

```python
from dollar import Dollar


def test_multiply_dollars():
    dollar = Dollar(5)
    dollar.times(2)
    assert dollar.amount == 10
    dollar.times(3)
    assert dollar.amount == 15


test_multiply_dollars()

```

- test fails
```python
Traceback (most recent call last):
  File "test.py", line 12, in <module>
    test_multiply_dollars()
  File "test.py", line 9, in test_multiply_dollars
    assert dollar.amount == 15
AssertionError
```

- now changing the test to have another object for the product

```python
from dollar import Dollar


def test_multiply_dollars():
    dollar = Dollar(5)
    product = dollar.times(2)
    assert product.amount == 10
    product = dollar.times(3)
    assert product.amount == 15


test_multiply_dollars()

```

- test fails

```python
Traceback (most recent call last):
  File "test.py", line 12, in <module>
    test_multiply_dollars()
  File "test.py", line 7, in test_multiply_dollars
    assert product.amount == 10
AttributeError: 'NoneType' object has no attribute 'amount'
```
- returning product from `Dollar`

```python
class Dollar:
    def __init__(self, amount):
        self.amount = amount

    def times(self, multiplier):
        return Dollar(self.amount * multiplier)

```

- test passes

![](assets/0defbf15.png)

---

### Possibilities

*Method*: Team needs to have a consistent experience growing the design of the system, little by little so the mechanics of the transformation are well practiced

*Motive* - business importance of  the feature and have the courage to do seemingly impossible task

*Opportunity* - combination of comprehensive, confidence-generated tests, well-factored program makes possible to isolate design decision which helps the team to identify the few potential sources of errors 
 
### Thoughts
- How each test  cover a small increment of functionality
- How small and ugly the changes can be to make the new tests run
- How often the tests are run
- How many teensy-weensy steps make up the refactorings