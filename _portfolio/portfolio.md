---
title: "Calculator"
excerpt: "Project 1. Calculator with unlimited magnitude. Created with C++ using powershell. <br/><img src='/images/SuperCalc.png'>"
collection: portfolio
---

# Quick Overview
I worked on this project over the course of a few months during Spring 2025 as a project for my Data Structures class. The idea of it is to create a calculator that can calculate any number no matter how large, the only limitation being the avaliable memory on the computer you are using. How it works is instead of storing the numbers as integers, which uses 4 bytes of memory space, the numbers are stored as characters, using only 1 byte, and calculations are performed on the numbers using the ASCII value of the character.



# Creating My Own Vector

In this project, I created and utilized my own vector. It works exaclty the same way as the #include <vector> works. One difference being the vector[x] and vector.at(x) functions. In the #include <vector> heading, using vector[x] will not check first if x is within the range of the vector, and could lead to someone accessing a part of memeory that they shouldn't. In my function, vector[x] will check if that x is within range of the vector, and will throw and error if it is not.


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


#### void apply(void f(T))

This class also includes a few extra functions. This function will take a function as input, and apply that function to every part of the vector. 

There are two variations, one when the function takes a reference variable and one when it takes a regular variable.

```cpp 
      void apply(void f(T&))
      {
        for(int i = 0; i < num; i++)
        {
            f(data[i]);
        }
      }

     void apply(void f(T))
      {
        for(int i = 0; i < num; i++)
        {
            f(data[i]);
        }
      }
```




