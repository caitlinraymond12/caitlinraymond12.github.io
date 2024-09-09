---
title: "Calculator with Unlimited Magnitude"
excerpt: "Created with C++ using Windows Powershell. <br/><img src='/images/SuperCalc.png'>"
collection: portfolio
---

## Quick Overview
I worked on this project over the course of a few months during Spring 2024 as a project for my Data Structures class. The idea of it is to create a calculator that can calculate any number no matter how large, the only limitation being the avaliable memory on the computer you are using. How it works is instead of storing the numbers as integers, which uses 4 bytes of memory space, the numbers are stored as characters, using only 1 byte, and calculations are performed on the numbers using the ASCII value of the character.



## Creating My Own Vector

In this project, I created and utilized my own vector. It functions the same way as the #include <vector>, with a few adjustments. One difference being the vector[x] and vector.at(x) functions. In the #include <vector> heading, using vector[x] will not check first if x is within the range of the vector, and could lead to someone accessing a part of memeory that they shouldn't. In my function, vector[x] will check if that x is within range of the vector, and will throw and error if it is not.


```cpp

template<typename T>
  class vector
  { protected:
      int cap, num;
      T* data;
    public:
      vector()
      { cap = 0;
        num = 0;
        data = NULL; }

      vector(int c)
      { cap = c;
        num = 0;
        data = new T[cap]; }

      vector(const vector<T> & x)
      { cap = x.cap;
        num = x.num;
        data = new T[cap];
        for(int i = 0; i < num; i++)
          data[i] = x.data[i]; }

      vector(int c, T array1[])
      { cap = c;
        num = c;
        data = new T[cap];
        for(int i = 0; i < c; i++)
        { data[i] = array1[i]; }  }

      ~vector()
      { delete[] data; }

      void push_back(T x)
      { if(num >= cap)
          grow();
        data[num] = x;
        num += 1; }

      void pop_back()
      { if(num <= 0)
          throw string("empty vector");
        num -= 1; }

      T& back()
      { if(num <= 0)
          throw string("empty vector");
        return data[num-1]; }

      void grow()
      { if(cap <= 0)
          cap = 1;
        else
          cap *= 2;
        T* newdata = new T[cap];
        for (int i = 0; i < num; i++)
          newdata[i] = data[i];
        delete[] data;
        data = newdata; }

      T pop()
      { if(num <= 0)
          throw string("empty vector");
        num -= 1;
        return data[num]; }

      int size()
      { return num; }

      void clear()
      { num = 0; }

      T& at(int x)
      { return data[x]; }

      vector<T> & operator=(const vector & other)
      { if(this == &other)
          return *this;
        delete data;
        cap = other.cap;
        num = other.num;
        data = new T[cap];
        for(int i = 0; i < num; i++)
          data[i] = other.data[i];
        return *this; }

      T& operator[](int i)
      { if(i < 0 || i >= num)
          throw string("Out of Range");
        return data[i]; }

      bool empty()
      { if(num == 0)
          return true;
        return false; }

      T reduce(int f(T, T), T x)
      { T r = x;
        for(int i = 0; i < num; i++)
        { r = f(data[i], r); }
        return r; }

```


### void apply(void f(T))

This class also includes a few extra functions. This function will take a function as input, and apply that function to every part of the vector. 

There are two variations, one when the function takes a reference variable and one when it takes a regular variable.

```cpp 
      void apply(void f(T&))
      { for(int i = 0; i < num; i++)
            f(data[i]);
      }

      void apply(void f(T))
      { for(int i = 0; i < num; i++)
            f(data[i]);
      }

```
## Creating a "bigint" Type

The way this calculator works is instead of storing the numbers as integers they are stored as characters, and all the normal operations that you performed on ints are now performed on the ASCII code that each char has. 

To do this, I created my own bigint class that does just that, and implemented that as the type that all the numbers are stored as. Then, I #include "bigint.h" as a header in my main calculator .cpp file. 

Any number referred to as a "bigint" is a number that is being stored as a char value.

```cpp
#include <string>
#include <iostream>
#include <fstream>
#include <sstream>
#include <iomanip>
#include <cctype>

using namespace std;

typedef unsigned char uchar;


class bigint
{ protected:
    int cap, num;
    bool neg;
    uchar *data;

  public:
    bigint();
    bigint(const bigint & other);
    bigint(const string & user_num);
    bigint(int n);
    ~bigint();
    bigint & operator=(int n);
    bigint & operator=(const bigint & other);
    bigint & operator=(const string & user_num);
    void remove();
    uchar get(int pos) const;
    void set(int pos, int newnum1);
    void grow();
    void print(ostream & out) const;
    void niceprint(ostream & out) const;
    int compare(const bigint & other) const;
    int compare2(const bigint & other) const;
    int toint();
    void shift(int step);
    void reverse();
    void clear();
    static void uadd(const bigint & a, const bigint & b, bigint & r);
    static void usub(const bigint & a, const bigint & b, bigint & r);
    static void bigmult(const bigint & a, const bigint & b, bigint & r);
    static void mult(const bigint & a, const int & b, bigint & r);
    static void add(const bigint & a, const bigint & b, bigint & r);
    static void subtract(const bigint & a, const bigint & b, bigint & r);
    static void division(const bigint & a, const int & b1, bigint & r);
    static bigint modulo(const bigint & a, const int & b);
    static void bigdivision(const bigint & a1, const bigint & b1, bigint & r);
    static bigint bigmodulo(const bigint & a1, const bigint & b1);
    bigint operator+(const bigint & b);
    bigint operator-(const bigint & b);
    bigint operator*(const bigint & b);
    bigint operator*(const int & b);
    bigint operator/(const int &b);
    bigint operator/(const bigint &b);
    bigint operator%(const int &b);
    bigint operator%(const bigint &b);
    void operator+=(const int &b);
    void operator+=(const bigint &b);
    void operator++(int);
    void operator--(int);
    void operator-=(const int &b);
    void operator-=(const bigint &b);
    void operator*=(const bigint &b);
    void operator*=(const int &b);
    void operator/=(const bigint &b);
    void operator/=(const int &b);
    void operator%=(const bigint &b);
    void operator%=(const int &b);
 };


ostream & operator<<(ostream & out, const bigint & value);

istream & operator>>(istream & in, bigint & variable);

bigint factorial(int n);
```

### Operations 

How every operation works is every function takes in three parameters, the first two "a" and "b" are the numbers to be added, subtracted, etc., and the last parameter "r" is the result of those two. All three are reference parameters, the first two are reference as to save time by not having to create two new bigints every time the function is called, and the third is a reference so the result will just be put right into the variable.


### Addition & Subtraction
Once two numbers are given, first they go through a function to determine if they are both postive, both negative, or one of each. 

From there, it's determined whether addition or subtraction will be used. For example, if we are to add a positive and a negative number, the subtraction function will be called.

These functions also determine if the final result will be negative or positive.  


Positive and Negative Tests:

```cpp
void bigint::add(const bigint & a, const bigint & b, bigint & r)
{ if(a.neg == false && b.neg == false)
    uadd(a, b, r);
  else if(a.neg == true && b.neg == false)
    usub(b, a, r);
  else if(a.neg == false && b.neg == true)
     usub(a, b, r);
  else if(a.neg == true && b.neg == true)
  { uadd(a, b, r);
    r.neg = true; } }

void bigint::subtract(const bigint & a, const bigint & b, bigint & r)
{ if(a.neg == false && b.neg == false)
    usub(a, b, r);
  else if(a.neg == true && b.neg == false)
  { uadd(a, b, r);
    r.neg = true; }
  else if(a.neg == false && b.neg == true)
    uadd(a, b, r);
  else if(a.neg == true && b.neg == true)
    usub(b, a, r);  }

```
Actual Functions 

```cpp

void bigint::uadd(const bigint & a, const bigint & b, bigint & r)
{ r.clear();
  int carry = 0;
  int n = max(a.num, b.num);
  for(int i = 0; i < n; i++)
  { int total = a.get(i) + b.get(i) + carry;
    if(total < 10)
    { r.set(i, total);
      carry = 0; }
    else
    { r.set(i, total - 10);
      carry = 1; } }
  r.set(n, carry);
  r.remove(); }

void bigint::usub(const bigint & a, const bigint & b, bigint & r)
{ r.clear();
  if(a.compare2(b) == -1)
  { usub(b, a, r);
    r.neg = true; }
  else
  { int borrow = 0;
    int n = max(a.num, b.num);
    for(int i = 0; i < n; i++)
    { int x = a.get(i) - b.get(i) - borrow;
      borrow = 0;
      if(x < 0)
      { x = x + 10;
        borrow = 1; }
      r.set(i, x); } }
   r.remove();}

```

### Division, Modulo, and Multiplication. 

There are two versions of all three of these functions.


The first version is between a bigint and another bigint:

#### Division 
```cpp
void bigint::bigdivision(const bigint & a1, const bigint & b1, bigint & r)
{ r.clear();
  if(b1.num == 1 && b1.data[0] == 0)
    throw string("Cannot Divide by 0");
  bigint a = a1;
  bigint b = b1;
  bigint c;
  for(int shift = a.num - b.num, i = 0; shift >= 0; shift--, i++)
  { b = b1;
    b.shift(shift);
    c = 0;
    int step = 0;
    while(true)
    { c = c + b;
      step = step + 1;
      if(c.compare(a) > 0)
      { c = c - b;
        step = step - 1;
        break; } }
    a = a - c;
    b = b / 10;
    r.set(i, step); }
  r.reverse(); }
```
#### Modulo 
```cpp
bigint bigint::bigmodulo(const bigint & a1, const bigint & b1)
{ if(b1.num == 1 && b1.data[0] == 0)
    throw string("Cannot Take Modulo of 0");
  bigint a = a1;
  bigint b = b1;
  bigint c;
  for(int shift = a.num - b.num, i = 0; shift >= 0; shift--, i++)
  { b = b1;
    b.shift(shift);
    c = 0;
    int step = 0;
    while(true)
    { c = c + b;
      step = step + 1;
      if(c.compare(a) > 0)
      { c = c - b;
        step = step - 1;
        break; } }
    a = a - c;
    b = b / 10; }
  return a; }
```

#### Multiplication
```cpp
void bigint::bigmult(const bigint & a, const bigint & b, bigint & r)
{ r.clear();
  if(a.neg != b.neg)
    r.neg = true;
  int carry = 0;
  for(int pos = 0; pos < a.num+b.num; pos++)
  { int total = 0;
    for(int i = 0; i <= pos; i++)
    { int j = pos - i;
      total += a.get(i) * b.get(j); }
    total += carry;
    int x = total % 10;
    r.set(pos, x);
    carry = total / 10; } }
```

The second version is between a bigint and an int:

#### Division 
```cpp
void bigint::division(const bigint & a, const int & b1, bigint & r)
{ r.clear();
  if(b1 == 0)
    throw string("Cannot Divide by 0");
  if(a.neg == true && b1 > 0 || a.neg == false && b1 < 0)
    r.neg = true;
  int b = b1;
  if(b1 < 0)
    b = -b1;
  int carry = 0;
  for(int i = a.num - 1, v = 0; i >= 0; i--, v++)
  { carry = carry * 10 + a.get(i);
    int x = carry / b;
    r.set(v, x);
    carry = carry % b; }
  r.reverse(); }
```
#### Modulo 
```cpp
bigint bigint::modulo(const bigint & a, const int & b)
{ if(b == 0)
    throw string("Cannot Take Modulo of 0");
  int carry = 0;
  for(int i = a.num - 1, v = 0; i >= 0; i--, v++)
  { carry = carry * 10 + a.get(i);
    carry = carry % b; }
  return bigint(carry); }
```

#### Multiplication
```cpp
void bigint::mult(const bigint & a, const int & b1, bigint & r)
{ r.clear();
  if(a.neg == true && b1 > 0 || a.neg == false && b1 < 0)
    r.neg = true;
  int b = b1;
  if(b1 < 0)
    b = -b1;
  int carry = 0;
  int total = 0;
  for(int i = 0; i < a.num || carry > 0; i++)
  { total = b * a.get(i) + carry;
    int x = total % 10;
    r.set(i, x);
    carry = total / 10; }
  r.remove();}
```

The reason for the difference is all operations are done in a very particular way, and cannot be generalized across different types. 

In particular, the mult() function between a bigint and an int is utilized when taking the factorial of something. This is because the factorial function continually multiplies an int by a bigint over and over again. It does this because it's more efficient to multiply a bigint by a regular int.

### Factorial 

```cpp
bigint factorial(int n)
{ if(n == 0)
    return bigint(1);
  if(n < 0)
    throw string("Cannot take Factorial of Negative");
  bigint r = 1;
  for(int i = 1; i <= n; i++)
  { r = r * i; }
  return r; }
```




## Building the Calculator 


Now that I've created my own vector and bigint class, it's time to create the actual calculator.  

It takes input from the user in Reverse Polish Notation, which means that the operator comes after the operand

### Example

```cpp
4 3 +
```
This would return the result "7", because 4 + 3 is equal to 7. 

```cpp
10 2 * 5 - 
```
This would return the result "15", because 10 * 2 = 20, and 20 - 5 = 15.

### User Input 
The following function is what allows the user to continually type in any math probelm they want the calculator to solve.

```cpp
void input()
{ bigint answer;
  while(true)
  { vector<string> data;
    string line;
    getline(cin, line);
    istringstream in(line);
    while(true)
    { string s;
      in >> s;
      if(in.fail())
        break;
      data.push_back(s); }
    try
    { answer = polish(data); }
    catch(string e)
    { string error = e;
      cout << error << "\n";
      continue; }
    answer.niceprint(cout); } }
```

In the input function, it takes in user input and adds it to a vector. That vector is then passed into the polish() fucntion, where is when all the calculating happens. 

In this function, it looks at every object in the vector. If it is an operator, it calls the appropiate operator. If it's a variable, the variable gets added to the vector var. If it is just a number, it gets added to the number vector. 

```cpp
bigint polish(vector<string> input)
{ nums.clear();
  bigint x, y;
  int i = 0;
  while(i < input.size())
  { string compare = input[i];

    if(compare == "=")
    { equals(input, i);
      continue; }

    else if(compare == "+"  || compare == "-"  ||
            compare == "*"  || compare == "/"  ||
            compare == "%"  || compare == "?" )
      op(compare, x, y);

    else if(compare == "+="  || compare == "-="  ||
            compare == "*="  || compare == "/="  ||
            compare == "%=")
    { op2(compare, x, y);
      equals2(input[0], i); }

    else if(compare == "++" || compare == "--")
    { op3(compare, x);
      equals2(input[0], i); }

    else if(compare == "!")
    { x = nums.back();
      nums.pop_back();
      int a = x.toint();
      nums.push_back(factorial(a)); }

    else if(compare[0] >= 'a' && compare[0] <= 'z' ||
            compare[0] >= 'A' && compare[0] <= 'Z')
      var(compare);

     else
       num(compare);

  i = i + 1; }
  return period(); }
  ```

How all of the calculations work is it takes off either one number or two numbers from the "num" vector depending on the operator, performs the desired operation on it, and then puts the result back onto the number vector. 

## Unlimited Magnitude 

Because we are performing operations on chars, which only occupy a very small part of our memory, we are able to perform operations on huge numbers in an incredibly short amount of time. 

### Factorial of 700

If you were to try to find the factorial of 700 using various online calculators...



 <br/><img src='/images/Calculator.png'>
 #### Calculator.net

<br/><img src='/images/Desmos.png'>
 #### Desmos

 <br/><img src='/images/Google.png'>
 #### Google

 <br/><img src='/images/Symbolab.png'>
 #### Symbolab

... you would be disappointed. 

However, the calculator I created can calculator 700 ! in less than a second:
 <br/><img src='/images/700.png'>
