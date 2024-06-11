# JunitAndMockito Interview Questions
Junit and mockito testing implementations

1. what is Junit ?
2. what are are most commonly used annotations in junit ?
3. what are the different assertion methods in junit ?
4. what are the good practices to write test cases ?
5. what is mockito ?
6. what is mocking ?
7. why can't mock static methods in mockito ?
8. what is the use of @Ignore annotation ?
9. can we mock private methods ?
10. what is the use of verify method ?

# Assignment

1. Write JUnit tests for a string utility class.
Create a StringUtils class with methods: isPalindrome(String s), reverse(String s), and capitalize(String s).
Write JUnit tests to validate the correctness of these methods, including edge cases like empty strings and null inputs.

2. Create an AuthService class that has a method login(String username, String password).
The AuthService should depend on a UserRepository which has a method findByUsername(String username).
Use Mockito to mock the UserRepository in your tests for AuthService.

3.  Write tests for bank account operations.
Create a BankAccount class with methods: deposit(double amount), withdraw(double amount), and getBalance().
Write JUnit tests to cover these methods, including scenarios like overdrawn accounts and zero balance.

4. Write and test a prime number checker.
Create a PrimeChecker class with a method isPrime(int number) that returns true if the number is prime, and false otherwise.
Write JUnit tests to cover a range of cases, including prime numbers, non-prime numbers, and edge cases like number = 0, number = 1, and negative numbers.

5. Write and test an anagram checker.
Create an AnagramChecker class with a method isAnagram(String str1, String str2) that returns true if str1 and str2 are anagrams, and false otherwise.
Write JUnit tests to verify the correctness of the method, including edge cases with different string lengths, empty strings, and strings with special characters.

6. Write and test a palindrome number checker.
Create a PalindromeNumber class with a method isPalindrome(int number) that returns true if the number is a palindrome, and false otherwise.
Write JUnit tests to verify the correctness of the method, including edge cases like single-digit numbers, negative numbers, and large numbers.
