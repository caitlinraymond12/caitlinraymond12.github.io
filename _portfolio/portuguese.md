---
title: "Practice Portuguese Verbs"
excerpt: "Program that allows you to practice conjugated portuguese verbs. <br/><img src='/images/Portugual.png'>"
collection: portfolio
---


## The Idea

Why did I create this program? 

I created this program because I love learning new languages. I think its amazing learning what unique phrases different languages have, and how even within a language there are different words and expressions based on the region where it is spoken. I taught myself Spanish in the summer of 2023. How did I do it? I started with the verbs. I first learned how to conjugate the top 100 verbs in the present tense for yo, tu, ella/el/usted, nosotros, ellas/ellos/ustedes. Then I moved onto the preterite, then the past perfect, and I made my way through all the tenses, only studying the verbs. Once I knew the top 100, then the top 300 most used verbs, I began to read sentences, then articles, and eventually books, all started on my basic knowledge of the Spanish verbs.

The purpose of explaining my journey with learning Spanish was to highlight how I learned it by focusing on verbs. Learning the verbs and how to conjugate them allowed me to learn Spanish in a very efficent and effective way. So, when I decided that the next language I wanted to tackle was Portuguese, I decided I wanted to do it in the same way, start with the verbs. 

The issue with this approach, as I found out, is there is no program out there that allows you to just practice Portuguese verbs. When I learned Spanish, I used an app called Ella Verbs, which was for Spanish only. I could not find a program that was as effective that focuses on learning Portuguese verbs only. I knew this was the way I wanted to learn it, so since I couldn't find a suitable program for me online, I created it.  

## Structs and Hashtables

There are two structs in my program, one to represent a portuguese word, and one to represent an english word.

### Portuguese Struct

The portuguese struct contains a vector of english words. The reason why this is a vector instead of a simple string or a pointer to the english word is because some portuguese verbs mean multiple things in english. For example:

```
Dizer: To talk, to speak
Fazer: To do, to make
```

Therefore in order to reflect all of a words meanings, there is a vector of pointers to english words. 

The next part in the struct are strings. These strings have the infinitive of the verb in portuguese, such as "falar", and the next strings have it conjugated in the different personal tenses: 

```
firstPersonSingular: "falo"
secondPersonSingular:  "falas"
thirdPersonSingular: "fala"
firstPersonPlural: "falamos"
secondPersonPlural:  "falais"
thirdPersonPlural: "falam"
```

These translate to english as I speak, you speak, he/she speaks, we speak, you guys/you all speak, they speak.


The final part of this struct is a bool, that signals whether or not the verb is irregular. 

```cpp
struct portugueseword
{
    vector<englishword *> englishwords;
    string portuguese;
    string firstPersonSingular, secondPersonSingular, thirdPersonSingular,
    firstPersonPlural, secondPersonPlural, thirdPersonPlural;
    bool irregular;

    portugueseword(vector<englishword *> e, string p, string fps, string sps, string tps, string fpp, string spp, string tpp, bool i)
    {
        englishwords = e;
        portuguese = p;
        firstPersonSingular = fps;
        secondPersonSingular = sps;
        thirdPersonSingular  = tps;
        firstPersonPlural = fpp;
        secondPersonPlural = spp;
        thirdPersonPlural = tpp;
        irregular = i;
    }

};

```



### English Struct

The english struct contains a pointer to a single portuguese verb that it can be translated to. 

It also contains strings that hold what the word is, "to talk", as well as it conjugated in all the personal tenses.

Something interesting about English is we do actually conjugate, just as many other languages do, just not to the same extent. 

For example, "I talk" and "She talks". The infinitve of the verb "To Talk" is being conjugated here, "talk" for first person singular, and "talks" for third person singular. However, the conjugation is often the same between different personal tenses, which is why in English we have to use pronouns such as "I", "We", "She", while other languages don't have to. 

```
firstPersonSingular: "talk"
secondPersonSingular:  "talk"
thirdPersonSingular: "talks"
firstPersonPlural: "talk"
secondPersonPlural:  "talk"
thirdPersonPlural: "talk"
```
With the appropiate pronouns these become I talk, you talk, he/she talks, we talk, you all/you guys talk, they talk.



```cpp
struct englishword
{
    string english;
    string firstPersonSingular, secondPersonSingular, thirdPersonSingular,
    firstPersonPlural, secondPersonPlural, thirdPersonPlural;
    portugueseword * portuguese;


    englishword(string e, string fps, string sps, string tps,
                string fpp, string spp, string tpp, portugueseword * p = nullptr)
    {
        english = e;
        firstPersonSingular = fps;
        secondPersonSingular = sps;
        thirdPersonSingular  = tps;
        firstPersonPlural = fpp;
        secondPersonPlural = spp;
        thirdPersonPlural = tpp;
        portuguese = p;
    }
};

```

