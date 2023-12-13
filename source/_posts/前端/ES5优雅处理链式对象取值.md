---
title: ES5优雅处理链式对象取值
date: 2023-12-13 19:26:58
updated: 2023-12-13 19:26:58
tags:
  - ES5
---
```javascript
function getValue(obj, props) {
    var propsArray = props.split('.');
    var currentProp = propsArray[0];
    var restProps = propsArray.slice(1);
    if (obj && obj.hasOwnProperty(currentProp)) {
      if (restProps.length === 0) {
        return obj[currentProp];
      } else {
        return getValue(obj[currentProp], restProps.join('.'));
      }
    }
    return undefined;
  }
  // 使用示例：
  var obj = {
    person: {
      name: "John",
      age: 30,
      address: {
        city: "New York",
        country: "USA"
      }
    }
  };
  var name = getValue(obj, 'person.name'); // "John"
  var age = getValue(obj, 'person.age');   // 30
  var city = getValue(obj, 'person.address.city'); // "New York"
  var country = getValue(obj, 'person.address.country'); // "USA"
  var salary = getValue(obj, 'person.salary'); // undefined
```