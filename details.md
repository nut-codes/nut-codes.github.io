---
layout: page
title: Technical Details
permalink: /details/
---

A Nut Code encodes the nutrition information of a Food or Serving.

A **Food** is a unitless representation of the ratios between the overall mass of food and the mass of its constituent nutrients. For purposes of encoding, the overall mass of the food is assumed to be 255 grams. 

A **Serving** is a Food plus a Serving Size.

A **Serving Size** represents the mass of a single serving of the food. When omitted, the serving size is assumed to be 100 grams. 

A **Serving Description** represents the type and quantity of units of food that make up a serving (such  as "3 cookies"). 

## Anatomy of a Nut Code

A Nut Code is a [URL](https://en.wikipedia.org/wiki/URL)-like [URI]((https://en.wikipedia.org/wiki/URL) consisting of a `nut://` prefix followed by base-64-encoded binary data. The use of a URL-like format allows a compatible reader app to register for the `nut` scheme and be automatically launched on many platforms when following a Nut Code link or scanning a QR code that resolves to a Nut Code. 

## Binary Encoding

The binary data encoded in a Nut Code consists of three parts:

1. A one-byte version number
2. Eight bytes corresponding to the "standard" nutrients:
    1. Total Fat
    2. Carbohydrates
    3. Protein
    4. Dietary Fiber
    5. Sugars
    6. Saturated Fat
    7. Monounsaturated Fat
    8. Polyunsaturated Fat
3. Zero or more two-byte extended codes consisting of a key byte and value byte

## Standard Nutrients

The standard nutrients were chosen for the following reasons:

- They can be represented to the recommended resolution (1 gram per serving, except for small amounts of fat at 0.5 grams) as a quantity between 0 and 255 grams. 
- They are present in most foods.
- They can be base-64 encoded, along with the version, into six ASCII characters.

## Extended Codes

Most extended codes represent micronutrient quantities. The key corresponds to a particular micronutrient, and the value is a logarithmic encoding of the quantity. The logarithmic encoding was chosen primarily for compactness. The smallest floating point number with wide support uses four bytes, and even half-precision floating point numbers can represent a much wider range than is needed. For the smallest trace nutrient values, the minimum quantity might be a tenth of a microgram and the maximum quantity is 255 grams. Logarithmic encoding in a single byte can represent values from 100 nanograms to 255 grams to within 10% of the encoded value. 

Currently the only other extended codes represent serving sizes and serving descriptions (TBD). 

Future extended codes could include information about allergens and dietary restrictions such as vegetarian, vegan, kosher, halal, etc. 

### Extended Code Dictionary

| Code | Nutrient       | Minimum Representable Mass |
|------|----------------|-------------------------------:|
|00|Water|1 g|
|09|Cholesterol|1 mg|
|0A|Added Sugar|1 g|
|0B|Sugar Alcohols|1 g|
|20|Vitamin A|1 µg|
|21|Thiamin|1 µg|
|22|Riboflavin|1 µg|
|23|Niacin|10 µg|
|24|Pantothenic Acid|100 µg|
|25|Vitamin B6|1 µg|
|26|Biotin|10 µg|
|27|Vitamin B12|10 ng|
|28|Vitamin C|100 µg|
|29|Vitamin D|10 ng|
|2A|Vitamin E|10 µg|
|2B|Vitamin K|100 ng|
|2C|Folate|1 µg|
|2D|Choline|1 mg|
|30|Calcium|1 mg|
|31|Chloride|1 mg|
|32|Iron|1 mg|
|33|Magnesium|1 mg|
|34|Phosphorus|1 mg|
|35|Potassium|10 mg|
|36|Sodium|1 mg|
|37|Zinc|10 µg|
|38|Chromium|100 ng|
|39|Copper|1 µg|
|3A|Iodine|1 µg|
|3B|Manganese|10 µg|
|3C|Molybdenum|100 ng|
|3D|Selenium|100 ng|
|3E|Fluoride|10 µg|
|D0|Caffeine|1 mg|
