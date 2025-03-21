---
title: "Shortest Path"
excerpt: "Program that will find the shortest distance between any two places in the continental United States. <br/><img src='/images/ShortestPath.png'>"
collection: portfolio
---


## Overview
This is a project that was completed on throughout the entire of my Fall 2024 semester as a project for my Algorithms class. 

The main purpose of this project is to allow the user to type in any two places in the continental United States, and the program will utilize Dikstra's Algorithm to find the shortest distance between these two places. Then, the program will print out instructions in a readable form, as well as print a map of the United States and draw a red path connected one place to the other.

The maps are binary files. Each must be read byte by byte and assigned a color based on the byte. In order to connect all the cities, roads, and intersections, three different files were used in order to create 

## Reading in Files

The three files that were read in were intersections.txt, connections.txt, and namedplaces.txt. 


First Three Lines of connections.txt:

> US-41/GA-3 T-- 22471 22060 18.225   
> US-27/GA-1 P-- 22192 22486 17.849  
> I-85/GA-403 L-- 22381 22294 7.206  

Each connection has the name of road, type of road, intersection A, intersection B, length(miles)


First Three Lines of namedplaces.txt:

> 62081025KSZurich                                               126      0.172221 39.232810 -99.43481416396  7.3237  
> 21987690IAZwingle                                              100      0.156037 42.297837 -90.687038 9097  5.7488  
> 62283685LAZwolle                                              1783      3.226466 31.635134 -93.64256026884  8.6067  

Each named place has a numeric code, state abbreviation, name (may contain spaces), population, area (square miles), latitude, longitude, intersection number of intersection closest to center, distance from center to intersection (miles)


First Three Lines of intersections.txt:

> -80.1415   26.7097    0.75 FL Plantation Mobile Home Park  
> -80.6534   26.8142    0.40 FL Fremd Village-Padgett Island  
> -80.2008   26.7109    1.63 FL Royal Palm Beach  

Each intersection has an intersection number (line it is in the file), longitude, latitude, distance to nearest named place, state of named place, name of named place.

Each of these files was read in using the functions readconnections(), readplaces(), and readintersections(),  respectively. 


### readconnections()

```cpp
void readconnections()
{
  string name, type;
  double length;
  int indexA, indexB;
  intersection * A, * B;
  ifstream file("connections.txt");

  while(true)
  {
    file >> name >> type >> indexA >> indexB >> length;
    if(file.fail())
    {
      break;
    }

    A = intersections[indexA];
    B = intersections[indexB];
    length *= 10000;
    connection * c = new connection(name, type, int(length), A, B);
    A->set_road(c);
    B->set_road(c);
  }
}

```

In the read connections function, the variables needed for each connection are created. The point of this file is to create a link between two intersections, so there are also pointers to two different intersections. Inside the connections file there are two numbers that tell which intersections the connection is for. Those two numbers are used to index a previously made intersections vector, and then those intersections have their roads that were originally set to null now set to this newly created connection.


### readplaces()

```cpp
void readplaces()
{
  int code, road, population;
  string state, city1;
  double area, lat, longi, distance;
  ifstream file("namedplaces.txt");
  while(true)
  {
    string text;
    getline(file, text);
    if(file.fail())
    {
      break;
    }
    state = text.substr(8, 2);
    city1 = text.substr(10, 50);
    string city;
    for(int i = 0; i < city1.size()-1; i++)
    {
      if(city1[i] == ' ' && city1[i+1] == ' ')
        break;
      city += city1[i];
    }
    string input;
    char * c;
    input = text.substr(0, 8);
    code = strtol(input.c_str(), &c, 10);

    input = text.substr(58,9);
    population = strtol(input.c_str(), &c, 10);

    input = text.substr(101, 6);
    road = strtol(input.c_str(), &c, 10);

    input = text.substr(67, 14);
    area = strtod(input.c_str(), &c);

    input = text.substr(81, 10);
    lat = strtod(input.c_str(), &c);

    input = text.substr(91, 11);
    longi = strtod(input.c_str(), &c);

    input = text.substr(107, 8);
    distance = strtod(input.c_str(), &c);

    place * p = new place(code, road, population, state, city, area, lat, longi, distance);
    HT.record(p);
    }
}
```

In order to read places, first the necessary variables are defined. Then, the file is read line by line. There aren't guarenteed spaces between each of the different sections in the file, so it's necessary to use text.subtr(x, length) in order to pull the data. 

There is a loop for city names in order to be able to properly read cities that might have a space in the name, such as "New York City."

### readintersections()

```cpp
void readintersections()
{
  int number;
  double lat, longi, distance;
  string state, place;
  ifstream file("intersections.txt");

  int line = 0;
  while(true)
  {
    string text;
    getline(file, text);
    if(file.fail())
    {
      break;
    }
    longi = stod(text.substr(0, 9));
    lat = stod(text.substr(10, 9));
    distance = stod(text.substr(21, 5));
    state = text.substr(28,2);
    place = text.substr(31, 20);
    intersection * i = new intersection(line, lat, longi, distance, 999999999, state, place, nullptr, nullptr);
    intersections.push_back(i);
    line++;
  }
}

```

This function starts by defining the necessary variables, the intersection number, latitude, longitude, distance to nearest named place, state of place, and name of place. Once the intersection object is made, it is pushed back to the intersections vector, that is used in the read connections function. 

When creating the new intersection object, the fifth parameter is set to the value 999999999. It is set to this value because when Dikstra's algorithm is used, the distance to every other node should be set to a large number so that the first time it is seen, the distance to it will always be smaller than this number. 

The last two parameters are set to null. The first is the previous intersection, which is set when the path is being explored and used for back tracking. The second is the road that connects this intersection to another. This is later set to a road int he read connections function.

As mentioned before, the intersection number is the line number that it is on, so line is set to 0 at the start and updated by one after every line is read.



