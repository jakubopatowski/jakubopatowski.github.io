---
layout: post
title: "Smart pointers in C++: unique pointer"
date: 2020-11-18
categories: c++
---

## Properties:
 
First of all what are main properties of ```std::unique_ptr```:
* There can be only one instance of created object, no copies
* Object held by ```std::unique_ptr``` is destroyed automaticaly when:

    * memory scope ends,
	* object which holds this pointer as its propertie is destroyed,
	* reset metod is invoked,
	* other object is assigned by ```std::make_unique``` (since C++14).

To show lifecycle of an object, we will use simple Foo class. Object of this class will show us moment of creation and destruction.

```c++
#pragma once

#include <string>
#include <iostream>
using namespace std;

class Foo {
  public:
    Foo( int no ) : _no( no ) {
        cout << "Invoked Foo-" << _no << " constructor.\n";
    };
    
    virtual ~Foo() { cout << "Invoked Foo-" << _no << " desctructor.\n"; };

    string getNo() const {
        return "Foo-" + to_string( _no );
    }; // ToDo:: rvalue reference

  private:
    int _no;
};
```

Now we can see ```std::unique_ptr``` in action:


```c++
#include <iostream>
#include <memory>
#include <string>
using namespace std;

#include "foo.hpp"

int main() {
    std::unique_ptr< Foo > foo( new Foo( 1 ) );
    cout << foo->getNo() << '\n';
    foo = std::make_unique< Foo >( 2 );
    cout << ( *foo ).getNo() << '\n';
    // cout << *foo.getNo() << '\n'; // error, cheeck operator precedence

    if ( foo ) {
        cout << "Foo object exists, it holds no. " << ( *foo ).getNo() << '\n';
    }
    foo.reset();
    
    if ( !foo ) {
        cout << "There is no Foo object." << '\n';
    }

    cout << "Main function ends here." << '\n';
    return 0;
}
```

```console
Invoked Foo-1 constructor.
Foo-1
Invoked Foo-2 constructor.
Invoked Foo-1 desctructor.
Foo-2
Foo object exists, it holds no. Foo-2
Invoked Foo-2 desctructor.
There is no Foo object.
Main function ends here.
```

