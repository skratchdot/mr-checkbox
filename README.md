Mr. Checkbox
============

[![Build Status](https://travis-ci.org/ryanaghdam/mr-checkbox.svg?branch=master)](https://travis-ci.org/ryanaghdam/mr-checkbox)

Validates a value against a given set of requirements.  Named after a former
colleague.

Mr. Checkbox (the function, not the person) is curried; validators can be
created with partially-applied invocation.


Domain
------

A `Requirement` is an object with an `error` and `validator`.  An
`error` is any value, usually a string, that is used to identify any values
that fail to meet the requirement.  A `validator` is a function that accepts a
value as input and returns `true` if the requirement is met; `false` if it is
not met.

```
{
  error: 'minimumLength',
  validator: function (value) { return value.length > 8; }
}
```

Mr. Checkbox produces an array of `error` values.  If the string `abc` were
validated against the requirements above, the result would be:
`['minimumLength']`.


Usage & Examples
----------------

Using partial-application.

```javascript
var mrCheckbox = require('mr-checkbox');

function isPalindrome(value) {
  return value === value.split('').reverse().join('');
}

var validatePassword = mrCheckbox([
  {
    error: 'Password must 8 characters or longer',
    validator: function (value) { return value.length > 7; }
  },
  {
    error: 'Password must be 50 characters or shorter',
    validator: function (value) { return value.length < 51; }
  },
  {
    error: 'Password cannot be "password"',
    validator: function (value) { return value !== 'password'; }
  },
  {
    error: 'Password cannot start with the letter Q.',
    validator: function (value) { return value.charAt(0) !== 'Q'; }
  },
  {
    error: 'Password must be a palindrome.',
    validator: isPalindrome
  }
]);

validatePassword('password');
  =>  [
        'Password cannot be "password"',
        'Password must be a palindrome.';
      ]


validatePassword('abc');
  =>  [
        'Password must 8 characters or longer',
        'Password must be a palindrome.'
      ]

validatePassword('QhannahhannahQ');
  =>  [ 'Password cannot start with the letter Q.' ]

validatePassword('iiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiii');
  =>  [ 'Password must be 50 characters or shorter' ]

validatePassword('hannahhannah');
  => []

```


Or, non-curried invocation.

```javascript
var mrCheckbox = require('mr-checkbox');

function isPalindrome(value) {
  return value === value.split('').reverse().join('');
}

var requirements = [
  {
    error: 'Password must 8 characters or longer',
    validator: function (value) { return value.length > 7; }
  },
  {
    error: 'Password must be 50 characters or shorter',
    validator: function (value) { return value.length < 51; }
  },
  {
    error: 'Password cannot be "password"',
    validator: function (value) { return value !== 'password'; }
  },
  {
    error: 'Password cannot start with the letter Q.',
    validator: function (value) { return value.charAt(0) !== 'Q'; }
  },
  {
    error: 'Password must be a palindrome.',
    validator: isPalindrome
  }
];

validatePassword(requirements, 'password');
  =>  [
        'Password cannot be "password"',
        'Password must be a palindrome.';
      ]


validatePassword(requirements, 'abc');
  =>  [
        'Password must 8 characters or longer',
        'Password must be a palindrome.'
      ]

validatePassword(requirements, 'QhannahhannahQ');
  =>  [ 'Password cannot start with the letter Q.' ]

validatePassword(requirements, 'iiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiii');
  =>  [ 'Password must be 50 characters or shorter' ]

validatePassword(requirements, 'hannahhannah');
  => []

```
