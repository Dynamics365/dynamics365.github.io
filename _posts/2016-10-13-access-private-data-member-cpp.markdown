---
title: Access private member data in C++
layout: post
date: 2016-10-13 21:25:00 +07:00
categories: programming
tags: [cpp, trick, template]
---

```cpp
#include <iostream>

template <class Tag, typename Tag::type Value>
struct PrivateRobber {
  friend typename Tag::type get(Tag) {
    return Value;
  }
};

class Item {
public:
  Item(int value) : m_value(value) {
  }
  friend std::ostream& operator<<(std::ostream& os, const Item& item) {
    return os << item.m_value;
  }
private:
  int m_value;
};

struct TagIntItem {
  // define `type` as type `int Item::*`
  typedef int Item::*type;
  // friend declaration for code from line 5 to line 7
  friend type get(TagIntItem);
};

template struct PrivateRobber<TagIntItem, &Item::m_value>;

int main()
{
  Item item(1);
  std::cout << "Item: " << item << "\n";
  // call function get in line 5, return an object of type `int Item::*`
  // a.k.a. `TagIntItem::type`, then dereference it with item
  int &value = item.*get(TagIntItem());
  std::cout << "Value: " << value << "\n";
  std::cout << "Changing value to 10...\n";
  value = 10;
  std::cout << "Changed value to 10...\n";
  // No matter how code will be reordered,
  // this line will be executed after changing variable.
  std::cout << "Item: " << item << std::endl;
  return 0;
}
```

Credited to [http://bloglitb.blogspot.com/2011/12/access-to-private-members-safer.html][1]


  [1]: http://bloglitb.blogspot.com/2011/12/access-to-private-members-safer.html
