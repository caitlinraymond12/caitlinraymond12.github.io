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


In addition to these three files that were used to connect all the cities in the United States, there was one file that included all the state abreviations and the corresponding state name.

### readstate()

```cpp
void readstates()
{
  string state, fullstate;
  ifstream file("states.txt");

  while(true)
  {
    string text;
    getline(file, text);
    if(file.fail())
    {
      break;
    }
    state = text.substr(0,2);
    stateabr.push_back(state);
    fullstate = text.substr(3, 30);
    statename.push_back(fullstate);
  }
}

```
In the intersections file, the state names are given as the abbreviation. However, for the final product that this program produces we want the full state names, so this program creates a vector of the actual names of the state.


## User Input

The function user() is what allows the user to enter their input. First, it defines all the necessary variables. Then, it goes into two while loops in order to accept the starting city and end city. 

```cpp

void user()
{
  place * s, * e;
  intersection * inter, * other;
  connection * r;
  vector<int> closed;

  while(true)
  {
    s = place_input("Starting");
    if(s != nullptr)
      break;
  }
  while(true)
  {
    e = place_input("End");
    if(e != nullptr)
      break;
  }


```

The reason why it goes into while loops is to allow the program to continue even if the user types in an incorrect city name. It will continue to run until they have entered two cities successfully. 

The function that these while loops calls is place_input(), and it returns place. 

### place_input()

``` cpp
place * place_input(string start_end)
{
    string city, state;
    place  * p;
    vector<string> v;

    cout << "Enter " << start_end << " City: ";
    getline(cin, city);
    if(city.empty())
        exit(1);
    HT.placename(city, v);

    if(v[0] == "NA")
        return nullptr;
    cout << "State Choices: ";
    for(int i = 0; i < v.size(); i++)
    {
        cout << v[i] << " ";
    }
    cout << "\n";
    cout << "Select A State: ";
    getline(cin, state);
    if(state.empty())
        exit(1);
    p = HT.search(city, state);
    if(p == nullptr)
    {
        cout << "Place Not Recognized.\n";
        return nullptr;
    }
    cout << "\n";
    return p;
}

```

The parameter takes in a string, and all it does is change the output to say either "Enter Starting City:" or "Enter Ending City."

In order to find the place the user wants, first the user types in a city. In this program, I defined my own hashtable class that has the method placename(), that will take in a city and return a vector of all the states that have a city with that name. This method is called with the city that the user typed in. 

Then, the program prints out a list of states for the user to choose from. The user selects a state. 

Once the user has selected their city and state combo, the program uses the hahstable method search() that will return a pointer to a place if one exists, and a nullptr if it doesn't. 

After this function is called twice, the program now has a pointer to a starting place, and a final destination, and the algorithm to find the shortest path can begin. 


```cpp

  intersection * start = intersections[s->road];
  intersection * end = intersections[e->road];
  if(start->get_line() == end->get_line())
  {
    cout << "Start and End Same. Total Distance 0 Miles.\n";
    exit(-1);
  }
  start->set_distance(0);
  Q.push(start);
  while(!Q.empty())
  {
    intersection * curr = Q.pop();

    if(curr->get_line() == end->get_line())
    {
        cout << "Found! Total Distance: " << curr->get_distance() / 10000 << "\n";
        break;
    }
    if(search(closed, curr->get_line()))
        continue;

    for(int i = 0; i < curr->get_connections_size(); i++)
    {
        intersection * other = curr->get_road(i)->get_there(curr);
        int newdist = curr->get_distance() + curr->get_road(i)->get_length();
        if( newdist < other->get_distance())
        {
            other->set_distance(newdist);
            other->set_prev(curr);
            other->set_prev_road(curr->get_road(i));
            Q.push(other);
        }
    }
    closed.push_back(curr->get_line());
  }

```

To begin, the two places are compared to see if they are the same. If they are, the distance is 0 Miles and the program terminates. 

If they aren't, the start distance is set to zero, the start intersection is added to the priority queue, and the algorithm begins. The priority queue being user is another data structure that I created myself.

The loop continually pops an item off the priority queue, and checks if it is the destiantion. If it is, the total distance is printed and the next section executes. 

If it's not the destination, first it is checked to see if it is in the closed set. If it is, the loop continues. The closed set in this program is actually just a vector, so the fucntion search(closed, curr->get_line()) is used to check if it is in the set. 

```cpp

bool search(vector<int> v, int target)
{
  for(int i = 0; i < v.size(); i++)
  {
    if(v[i] == target)
    {
        return true;
    }
  }
  return false;
}

```

**NOTE** This is a project that was made for one of my classes in Fall 2024. Looking over it again months later, it is obvious that this is a very inefficent way of doing a set, and really it should be a hashtable of places, not a vector. 

If it's not in the closed set, another loop begins. This loop goes through all the roads in the current intersection, and finds what intersection they are connected to. Then, it calculates the distance to that intersection by going through this road. If it is shorter than the current distance to get there, it's distance is updated, and it is added to the queue. 

At the end of the loop, the current intersection is added to the closed set. 

After the loop is completed, we have successfully found a starting place and an ending place. The final section of this function prints out the instructions to get from one place to another. 

```cpp
  vector<double> coords;
  vector<connection *> route;
  vector<intersection *> inters;
  coords.push_back(start->get_lat());
  coords.push_back(start->get_long());
  backtrack(end, coords, route, inters);
  int prev = 0;
  string prevname = route[prev]->get_name();
  int prevlength = route[prev]->get_length();
  for(int i = 1; i < route.size(); i++)
  {
     string currname = route[i]->get_name();
     int currlength = route[i]->get_length();
     if(currname == prevname)
     {
         prevlength += currlength;
         continue;
     }
     cout << "Take " << prevname << " distance of "
          << double (prevlength) / 10000 << " miles to intersection #"
					<< inters[i-1]->get_line() << " " << inters[i-1]->get_nearest_dist() 
					<< " miles away from " << inters[i-1]->get_city() << ", " 
					<< inters[i-1]->get_state() << "\n";
     prev = i;
     prevname = currname;
     prevlength = currlength;
  }
  cout << "Take " << prevname << " distance of "
		<< double(prevlength) / 10000 << " miles to intersection #"
		<< end->get_line() << " " << end->get_nearest_dist() 
					<< " miles away from " << end->get_city() << ", "
					<< end->get_state() << "\n";
	drawmap(coords);
}


```

It first defines a vector of doubles that are the coordinates of each place. This will be used later when drawing the map. To begin, the latitude and longitude of the starting place is added. This vector is passed in as a parameter to the backtrack() function, and is updated as the program creates the path. 

``` cpp

void backtrack(intersection * end, vector<double> & coords, vector<connection *>& route, vector<intersection *>& inter)
{
    if(end->get_prev() == nullptr)
    {
        return;
    }
    backtrack(end->get_prev(), coords, route, inter);
    coords.push_back(end->get_lat());
    coords.push_back(end->get_long());
    route.push_back(end->get_prev_road());
    inter.push_back(end);

}

```

Each time an intersection was added to the queue, the intersection took to get there was attached to that object. This function goes backwards through the path, starting at the final destination, to build a path going from the starting intersection to the ending. 


Once backtrack is complete, we have a vector of coordinates of all the roads taken, a vector of connection * that contain all the connections taken, and a vector of intersection * that contain all the intersections taken. These three vectors are used to print out the instructions to go from one place to another. 

Finally, after the instructions are printed, drawmap() is called, and the route is printed on top of a map. 

```cpp
void drawmap(vector<double> coords)
{
	double minlat = coords[0], maxlat = coords[0], minlong = coords[1], maxlong = coords[1];
	for(int i = 2; i < coords.size(); i+=2)
	{
		if(coords[i] < minlat)
      minlat = coords[i];
		else if(coords[i] > maxlat)
	    maxlat = coords[i];
		
		if(coords[i+1] < minlong)
      minlong = coords[i+1];
		else if(coords[i+1] > maxlong)
	    maxlong = coords[i+1];
	}
  double map_minlat = 0, map_maxlat = 100, map_minlong, map_maxlong;
	string mapname;
  while(true)
  {
    double curr_minlat, curr_maxlat, curr_minlong, curr_maxlong;
    string currname;

    ifstream in("coverage.txt");
    while(true)
    {
        in >> curr_maxlat >> curr_minlat >> curr_maxlong >> curr_minlong >> currname;
        if(in.fail())
            break;
				curr_minlong = abs(curr_minlong);
				curr_maxlong = abs(curr_maxlong);

       if(curr_minlat <= minlat && curr_maxlat > maxlat &&
           curr_minlong < minlong && curr_maxlong > maxlong)
       {
            if(curr_maxlat - curr_minlat <= map_maxlat - map_minlat)
            {
                map_minlat = curr_minlat;
                map_maxlat = curr_maxlat;
                map_minlong = curr_minlong;
                map_maxlong = curr_maxlong;
                mapname = currname;
            }
       }
    }
    break;
  }
		string path = "all\\" + mapname;
    fstream map(path, ios::in | ios::binary);
		if(map.fail())
		{
			cerr << "Error Opening Map\n";
			return;
		}
		string row; 
		int rows, cols;
		short int pixel;
		getline(map, row);
		istringstream in(row);
		string word;
		while(true)
		{
			in >> word;
			if(in.fail())
				break;
			if(word == "rows")
				in >> rows;
			if(word == "columns")
				in>>cols;
		}
		make_window(cols, rows);
		map.seekg(2 * cols, ios::beg);
	  for(int y = 0; y < rows; y++)
		{
			for(int x = 0; x < cols; x++)
			{
				short int elevation;
				map.read( (char *) & elevation, sizeof(elevation));
			
				if(elevation < 0)
					set_pixel_color(x, y, color::blue);
				else
					set_pixel_color(x, y, make_color_hls(.333, double(elevation) / 9500 + .2, .5));
			}
		}
		int lat_mult = rows / (map_maxlat - map_minlat);
		int long_mult = cols / (map_maxlong - map_minlong);
		set_pen_color(color::red);
		set_pen_width(3);
		double longi = coords.back();
		coords.pop_back();
		double lat = coords.back();
		coords.pop_back();
		lat = (lat - map_minlat) * lat_mult;
		longi = (longi - map_minlong) * long_mult;
	  move_to(cols - longi, rows - lat);
		while(!coords.empty())
		{
			double longi = coords.back();
			coords.pop_back();
			double lat = coords.back();
			coords.pop_back();
			lat = (lat - map_minlat) * lat_mult;
			longi = (longi - map_minlong) * long_mult;
			draw_to(cols - longi, rows - lat);	
		}
}

```

In order to draw the map, functions from the library given by the professor of the class, *library.h* , were used. These functions included set_pen_color(color::red), set_pen_width(3), and other functions that aren't typically seen in with the standard library.

First, the minimum and maximum coordinates for latitude and longitude were found, which were used to select and appropiate map. This was useful because if I only wanted to go from Miami to Tampa, I don't want a map of the entire United States, I only want a map of that section of Florida. 

Once a map was chosen, it was read and colored in. The maps were binary files, so it was necessary to open them using *fstream map(path, ios::in | ios::binary);* . The first line of the file had how many rows and columns were in this certain file. Once the rows and columns were known, a window of appropiate size was created. 

Then, the file was read in byte-by-byte and painted in pixel-by-pixel. If the elevation was less than 0, the pixel was painted in blue. Else, it was painted in a color depending on it's elevation, with the higher elevations being lighter green and the lower elevations being darker green. 

Once the base of the map was drawn, it was time to draw the path on top of it. The function takes in a vector of coordinates as a parameter. This vector is read through, drawing a line between each pair of latitiude and longitude it was given. These coordinates are the starting positions of each road that was taken. 


### Final 

Once all of these functions were put together, and combined with two more files containing the created data structures headings and definitions, the program was completed!

It was possible to type in any two cities in the United States, and this program would find the shortest possible distance between the two, print out instructions, and visually draw it out on a map. 

If your interested in seeing the full program with all the files, head over to my github and there is a repositiory with all of my project that I've worked on. 

[Link to view project on GitHub](https://github.com/caitlinraymond12/projects/tree/main/shortest-path-on-map)

### Examples

Below are a few examples of user input and what the program output would be. The output for the directions has been shorted for each example as to not clutter the page. More examples can be found on my GitHub. 



#### Miami-Seattle

Enter Starting City: Miami
State Choices: AZ FL MO OK TX
Select A State: FL

Enter End City: Seattle
State Choices: WA
Select A State: WA

Found! Total Distance: 3040.92
Take I-95 distance of 1.43 miles to intersection #28869 1.9 miles away from Miami, FL
Take FL-112 distance of 3.379 miles to intersection #28871 1.5 miles away from Brownsville, FL
Take US-27 distance of 147.117 miles to intersection #28558 4.6 miles away from Sebring, FL
Take US-27/US-98 distance of 23 miles to intersection #28401 2.7 miles away from Frostproof, FL
Take US-27 distance of 79.7279 miles to intersection #27772 1.3 miles away from Leesburg, FL
Take US-27/US-441 distance of 19.039 miles to intersection #27680 0.4 miles away from Belleview, FL
Take US-27/US-301 distance of 11.092 miles to intersection #27621 0.9 miles away from Ocala, FL

...


Take I-90/US-93 distance of 5.225 miles to intersection #1293 0.3 miles away from Wye, MT
Take I-90 distance of 165.051 miles to intersection #672 1.3 miles away from Spokane, WA
Take I-90/US-2/US-395 distance of 2.642 miles to intersection #680 3.5 miles away from Spokane, WA
Take US-2 distance of 19.292 miles to intersection #628 0.2 miles away from Reardan, WA
Take US-2/WA-231 distance of 3.173 miles to intersection #629 2.9 miles away from Reardan, WA
Take US-2 distance of 36.9469 miles to intersection #553 0.4 miles away from Wilbur, WA
Take US-2/WA-21 distance of 0.623 miles to intersection #551 1 miles away from Wilbur, WA
Take US-2 distance of 71.466 miles to intersection #509 3.6 miles away from Entiat, WA
Take US-2/US-97 distance of 27.1559 miles to intersection #510 3.8 miles away from Leavenworth, WA
Take US-2 distance of 73.153 miles to intersection #363 0.6 miles away from Monroe, WA
Take WA-522 distance of 23.059 miles to intersection #384 3.8 miles away from Seattle, WA
Take I-5 distance of 4.586 miles to intersection #399 0.8 miles away from Seattle, WA



 <br/><img src='/images/Miami-Seattle.png'>"



#### Atlanta-Charleston

Enter Starting City: Atlanta
State Choices: GA IL IN KS LA MI MO NE TX
Select A State: GA

Enter End City: Charleston
State Choices: AR IL MO MS SC TN UT WV
Select A State: SC

Found! Total Distance: 274.24
Take US-29/US-78/GA-8 distance of 0.985 miles to intersection #23097 1.2 miles away from Atlanta, GA
Take US-29/US-78/US-278/GA-8 distance of 2.399 miles to intersection #23074 1.4 miles away from Druid Hills, GA
Take US-23/US-29/US-78/US-278/GA-8 distance of 1.651 miles to intersection #23075 1.1 miles away from Decatur, GA
Take US-278/GA-10 distance of 3.906 miles to intersection #23060 0.8 miles away from Avondale Estates, GA
Take US-278/GA-12 distance of 9.825 miles to intersection #23137 0.6 miles away from Lithonia, GA
Take I-20/US-278/GA-402 distance of 14.687 miles to intersection #23237 1.2 miles away from Oxford, GA
Take I-20/GA-402 distance of 107.298 miles to intersection #22857 3.2 miles away from Martinez, GA
Take GA-28 distance of 4.6609 miles to intersection #22901 2.5 miles away from North Augusta, SC
Take GA-28/bus-US-25/GA-121 distance of 1.131 miles to intersection #22905 2.8 miles away from North Augusta, SC
Take GA-28 distance of 3.402 miles to intersection #22945 4.5 miles away from Clearwater, SC
Take SC-28 distance of 1.6279 miles to intersection #22964 5.5 miles away from Clearwater, SC
Take US-278 distance of 23.721 miles to intersection #22957 4.7 miles away from Williston, SC
Take SC-39 distance of 4.625 miles to intersection #22860 0.1 miles away from Williston, SC
Take US-78 distance of 33.21 miles to intersection #22986 4.3 miles away from Branchville, SC
Take SC-61 distance of 40.183 miles to intersection #23236 6.6 miles away from Summerville, SC
Take alt-US-17/SC-61 distance of 0.484 miles to intersection #23233 6.2 miles away from Summerville, SC
Take SC-61 distance of 20.444 miles to intersection #23389 0.8 miles away from Charleston, SC


  <br/><img src='/images/Atlanta-Charleston.png'>"







