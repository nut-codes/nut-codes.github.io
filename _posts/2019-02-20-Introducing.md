---
layout: post
title: Introducing Nut.Codes
---

Nut.Codes is an experimental compact digital representation of nutrition information. 

# The Problem

For people keeping track of their macro- and micronutrient intake, some of the worst food options (heavily processed foods with lots of packaging) are also the easiest to track. With many food-diary apps, it's as simple as scanning a barcode. 

Meanwhile some of the best options for your health, pocketbook, and the environment (that is, food prepared at home from fresh ingredients that you can buy in bulk) are some of the hardest to track. You either have to manually transcribe the nutrition information (or use a potentially-unreliable OCR feature), or add up each ingredient in the right amount, remembering to adjust for serving size and any substitutions you might have made. 

# The Solution

Fortunately, nutrition information is not particularly information-dense. The [USDA national nutrition information database](https://ndb.nal.usda.gov) tracks 59 significant data points for each food. Even if each were encoded as a half-precision floating point number (which for most values is more precision than is warranted), the values for an ingredient or meal could be encoded in less than one kilobyte. 

This is well within the range of data that can be handled by a QR code or hypertext URI. These could then be used on:

* Recipe web sites
* Printed cookbooks
* Restaurant menus
* Product packaging
* Nutrition information databases

Someone using a food-tracking mobile app on their phone who has just finished cooking up a recipe could use their phone's camera to read a nut.code in QR code format, which could automatically open their food-tracking app. If they're reading the recipe from a web page on the same device that their food-tracking app is installed on, they could click a link that would open the app with the nutrition information pre-filled. In both cases they would simply have to enter a serving size if it isn't already specified in the code. 

A nut.code would also allow people with visual impairments to read nutrition information with the assistance of a smartphone. 

# Technical Overview

A nut.code uses a URL-like representation consiting of a `nut://` prefix followed by a base-64-encoded binary representation of the nutrition information. Although the prefix and encoding results in some degree of overhead, it allows the code to be directly inserted into HTML documents and provides greater compatibility with available barcode generators (many of which struggle with anything other than plain-text URLs) and scanners (for example the built-in camera app in iOS). 

All values are normalized against a 255-gram unit size. This allows macronutrients (and micronutrients whose minimum value is 1 gram) to be encoded as 8-bit unsigned integers, while retaining an accuracy of better than 0.4 percent. 

Nut.codes come in short- and long-form. 

The short form encodes the amount of fat, "net" carbohydrates (that is carbohydrates other than dietary fiber), and protein in a 255-gram unit of food. The values are concatenated, base-64 encoded, and prepended with a `nut://` prefix. 

The long form encodes the same information, which is followed by a variable-length list of multi-byte tuples, whose first byte indicates the type of value (for example, "micrograms of Vitamin D"), and whose subsequent bytes (for micronutrients) encode the amount, using a logarithmic mapping whose maximum representable value is 255 grams. The tuples can also encode metadata such as serving size, name, and (as a future enhancement) allergen content. 

## Examples

A food with no macronutrient content (such as water), would have three zero bytes, which results in `AAAA` when base-64 encoded, so the complete nut.code would be [nut://AAAA](nut://AAAA). 

A 1-cup (240 ml) serving of whole milk has 8 grams of fat, 12 grams of carbohydrate, and 8 grams of protein. When scaled to a 255-gram unit, the result is 9 grams of fat and protein and 14 grams of carbohydrates. This results in a complete short-form nut.code of [nut://CRQJ](nut://CRQJ). 

# Technical Details

Again, all values are normalized against a 255-gram unit. 

## Macronutrients

Macronutrients can be encoded as one byte each, since the weight of each must always be less than the total weight of (2^8 - 1) grams. Macronutrients are encoded in a position-based manner, in the order of fat, carbohydrates, and protein. 

## Vitamins and Minerals

The micronutrient table is typically a sparse one, so rather than pad it with a bunch of zeroes, each micronutrient will have a one-byte specifier and a one-byte quantifier. 

Most vitamins and minerals are measured in milligrams or micrograms, although a few are measured in IU or grams or tenths of micrograms. Thus the possible values could range as widely as from zero to 2.55 billion. For this reason a logarithmic scale will be used.

For each micronutrient, a minimum viable quantity (MVQ) will be chosen (based on a 255g serving size's precision in the USDA database). The maximum amount will be 255g. A quantifier of zero will correspond to one unit of the minimum viable quantity. Each increment of the quantifier would multiply the minimum viable quantity by a number (root) calculated as described below:

> root = e^ln(m / 256)

Where m is the number of MVQ units required to equal 255g.

Thus the amount expressed in the minimum viable quantity units would be:

> MVQs = root^(n + 1)

Where n is the quantifier (0-255) for that nutrient. To express a quantity of zero, the entry is omitted from the table. 

### Mineral Specifiers

| SP | Nutrient       | MVQ    | Root   |
| --- | -------------- | ------:| ------:|
| 00 | Calcium        |   1 mg | 1.0498 |
| 01 | Iron           |  10 µg | 1.0688 |
| 02 | Magnesium      |   1 mg | 1.0498 |
| 03 | Phosphorus     |   1 mg | 1.0498 |
| 04 | Potassium      |   1 mg | 1.0498 |
| 05 | Sodium         |   1 mg | 1.0498 |
| 06 | Zinc           |  10 µg | 1.0688 |
| 07 | Copper         |   1 µg | 1.0785 |
| 08 | Manganese      |   1 µg | 1.0785 |
| 09 | Selenium       | 100 ng | 1.0883 |
| 0A | Fluoride       | 100 ng | 1.0883 |

0B-0F: Reserved

### Vitamin Specifiers

| SP | Nutrient       | MVQ    | Root   |
| --- | -------------- | ------:| ------:|
| 10 | Vitamin C      | 100 µg | 1.0593 |
| 11 | Thiamin        |   1 µg | 1.0785 |
| 12 | Riboflavin     |   1 µg | 1.0785 |
| 13 | Niacin         |   1 µg | 1.0785 |
| 14 | Vitamin B-6    |   1 µg | 1.0785 |
| 15 | Folate, DFE    |   1 µg | 1.0785 |
| 16 | Vitamin B-12   |  10 ng | 1.0981 |
| 17 | Vitamin A, RAE |   1 µg | 1.0785 |
| 18 | Vitamin A, IU  |   1 IU |    ?   |
| 19 | Vitamin E, α-t |  10 µg | 1.0688 |
| 1A | Vitamin D, 2+3 | 100 ng | 1.0883 |
| 1B | Vitamin D, IU  |   1 IU |    ?   |
| 1C | Vitamin K      | 100 ng | 1.0883 |

### Serving Sizes

Serving sizes are not needed for bulk ingredients, but are strongly encouraged for foods that have a recommended serving size. Sizes are primarily specified as the number of 255-gram units that comprise a serving. This information is used to scale the nutrition information to the size of a serving, but results in a serving size that can only be displayed as a weight. 

For some foods, relating the serving size in more familiar terms is desirable. For example, to specify the serving size of a beverage, using units of volume may be more intuitive, or specifying a serving of cookies as a number of pieces 


