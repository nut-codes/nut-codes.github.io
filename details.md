---
layout: page
title: Technical Details
permalink: /details/
---

A **Nut Code** (short for "nutrition code") encodes the nutrition information of a meal or serving. 

The initial implementation (the **Nut Macro** code) only encodes macronutrients, that is to say carbohydrates, protein and fat. Carbohydrates should be encoded after subtracting dietary fiber from the total. Each value can be from 0 to 250 grams. Values of less than five grams have a precision of one half of a gram, and values between 10 and 250 grams have a precision of one gram. Meals with more than around 1000 calories may need to be treated as more than one meal. 

Nut Codes are encoded in a URL-like format. This adds somewhat to the overall size of the code, but it facilitates compatibility with existing operating systems. 

The format consists of a scheme (`nut`), some boilerplate (`://`) and a base-64-encoded string. The base-64-encoded portion should be encoded using a URL-safe encoding style (which subsititutes `-` for `+` and `_` for `/`).

For Nut Macro codes, the base-64-encoded string is four characters long, resulting in three bytes of data. These bytes correspond to carbohydrate, protein, and fat content, (in that order). 

Each byte is decoded as follows: For values of less than 10, the number of grams is the byte value divided by two. For byte values of greater than 10, the number of grams is the byte value minus five. 

The number of grams is encoded as follows: If the mass is less than five, divide it by two and round to the nearest integer. If the mass is greater than five, round it to the nearest integer and add five.

Future extensions may include micronutrient values and possibly things like allergens and other dietary restrictions (e.g. vegetarian, vegan, kosher). 
