---
layout: post
title:  "GSoC with Shogun"
date:   2016-09-01 00:00:00 +0530
---
![GSoC Logo](/assets/gsoc-logo.svg)

I recently worked with **[Shogun](https://github.com/shogun-toolbox/shogun)**, an opensource machine learning toolbox, on a **[Google Summer of Code project](https://summerofcode.withgoogle.com/projects/#6312557614727168)**. Shogun is written in C++ and offers numerous machine learning algorithms via supporting high level languages like Python, Lua, Ruby etc. However, Shogun is not very easy to build and has lots of dependencies. Most users install Shogun to use some particular algorithms instead of using all the algorithms that Shogun offers, which results in installation redundancies. Moreover, developers also have to build the entire library, again and again, to test new features which slows down the development process. These problems can be solved by a **plugin architecture**. A new **parameter framework** is required to build the plugin architecture, and it also helps to improve and cleanup the API of Shogun.

<img align="right" hspace="10" src="/assets/shogun-logo.png">

The goal of my project was -

1. To build the proposed parameter framework and plugin architecture.
2. Migrate old code base to use the new parameter framework and plugin architecture.
3. Contribute to Shogun's **[cookbook](http://shogun.ml/cookbook/latest/)** by writing API tutorials weekly.

I was mentored by [Sergey Lisitsyn](https://github.com/lisitsyn), [Heiko Strathmann](https://github.com/karlnapf) and [Viktor Gal](https://github.com/vigsterkr). The rest of the Shogun team ([Rahul De](https://github.com/lambday), [Esben Sørig](https://github.com/sorig)) was also very helpful, time to time. [Lea Goetz](https://www.linkedin.com/in/lea-goetz-211213116) managed GSoC 2016 for Shogun, organized all the meetings on the IRC and kept the [wiki](https://github.com/shogun-toolbox/shogun/wiki) updated. She also made sure that [Zora](https://github.com/oxphos), [Saurabh](https://github.com/saurabh7) and I stick to our planned timelines as much as possible. Thanks to all these amazing people for helping me with the project and making this an enjoyable experience.

Now I'll get into the technical details of my project.

## Parameter Framework

With the new framework, we aimed for the following:

- A consistent API to set or get the value of a parameter in any Shogun class, and allow the removal of innumerable setters and getters.
- Cleanup the Shogun base classes and modularize things for easier development.
- Work towards allowing plugins for Shogun.
- Faster [SWIG](http://swig.org/) compilation with less symbols being exported -- all parameters (public member variables) of a class will be stored in a `map` (like [`std::unordered_map`](http://en.cppreference.com/w/cpp/container/unordered_map)). So only the `map` would be exported in SWIG generated wrappers instead of all the individual parameters. Elimination of setters and getters will also reduce the functions exported in SWIG.

To make this more concrete, here is some code.

{% highlight cpp %}
// Instantiate an object of some shogun class
auto obj = Obj();
obj.set_name("gaussian");
obj.set_width(5);
// The above setters will be replaced by one function "set<T>()".
obj.set<std::string>("name", "gaussian");
obj.set<int32_t>("width", 5);
// Similarly, get_width() and get_name() will be replaced by "get<T>()".
std::string name = obj.get<std::string>("name");
int32_t width = obj.get<int32_t>("width");
{% endhighlight %}

Class `Obj` looks like,

{% highlight cpp %}
class Obj {
    std::unordered_map<std::string, Any> map;
public:
    template <class T>
    void set(std::string name, T value) {
        map[name] = Any(value);
    }

    template <class T>
    T get(std::string name) {
        return Any::recall_type<T>(map[name]);
    }
}
{% endhighlight %}

`Any` allows to store objects of arbitrary types in a type agnostic way. This makes it possible to store a variety of types in the `map`. Above code snippets are over-simplifications to make it easy to understand, actual implementation in Shogun is very different from this. I haven't also talked about the SWIG wrappers. For more details, look at [this readme](https://gist.github.com/sanuj/f27046a0658c4320325849a81649d139).

## Plugin architecture

Advantages of using a plugin architecture are:

- No need to recompile entire Shogun code but only the plugin that is being developed.
- Each plugin will have its own dependencies, so `base-shogun` will end up having minimal dependencies.
- We can have 'frozen' implementation of `base-shogun` that actually could be installed and never touched again. Plugins could change arbitrarily.
- Scientists can use Shogun again as the building process would be much easier.

I used [`dlopen`](http://linux.die.net/man/3/dlopen) to implement the plugin interface in Shogun. I can't see how to explain the plugin architecture implementation coherently without making this post too long. So, I'll just link to [a readme](https://gist.github.com/sanuj/f885cbd2d7946995af80953476d720fd) which has more details about the implementation.

## Cookbook

[Shogun Cookbook](http://shogun.ml/cookbook/latest) is a collection of API tutorials, which explain basic usage of algorithms available in the library. While it is still under development, almost all the tutorials were added during GSoC 2016. The tutorials are written in "meta-language" (which is like pseudo-code), the generator parses meta-language code and generates code in Python, Octave, Java, Ruby, R, C# and C++. More details about the cookbook can be found in [this readme](https://github.com/shogun-toolbox/shogun/tree/develop/doc/cookbook).

My commits and pull requests during GSoC 2016 are listed [here](https://gist.github.com/sanuj/62f576cdaa80ecaa781f78b4b9298de4).

I was able to build the plugin architecture and parameter framework successfully but migration of old code-base still remains incomplete. It was more complicated and time-consuming than I thought and this shows the importance of understanding the project profoundly before planning a timeline. Most of my time was spent writing unit-tests, writing cookbook pages and debugging, than actually adding new features. Some errors would take multiple days to debug which was really frustrating. But, debugging is a very important skill that a programmer should have and those experiences sure taught me some important things like - to narrow down the problem, avoid being lazy and use a debugger like gdb. It also taught me how to handle large code-bases, object oriented programming and wrapper functions (using C++ code in high-level languages like Python). I would sometimes feel like a C++ ninja after debugging compiler errors by using [SFINAE](http://en.cppreference.com/w/cpp/language/sfinae). This was an excellent learning experience for me and I got to work with people from different countries (UK, Russia, Singapore, Germany) and cultures. I highly recommend students to participate in Google Summer of Code by looking for projects that interest them because having opensource experience is highly beneficial, especially for programmers.

Lastly, a shout out to Google for funding all the projects and promoting opensource among students.
