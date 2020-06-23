---
layout: page
title: Frequently Asked Questions
permalink: /faq/
---

## Q: What recipe apps support Nut Codes?

A: Currently there are no recipe apps that support Nut Codes. If your app adds support, please [reach out on Twitter](https://twitter.com/nutcodes). 

## Q: What food diary apps support Nut Codes?

A: Currently there are no food diary apps that support Nut Codes. If your app adds support, please [reach out on Twitter](https://twitter.com/nutcodes). 

## Q: Are there any example implementations?

A: There is [an implementation written in Swift](https://github.com/nut-codes/swift) that supports encoding and parsing of version 1 Nut Codes. Contributions of implementations in additional languages are welcome. 

## Q: Does the Nut Code encoding lose precision?

A: Yes, but probably not enough to matter. For macronutrients, the worst-case error is 0.4 grams, which is around 0.1% of a 2000-calorie daily intake. For micronutrients, the worst-case error for any given food is less than 10% of the encoded value. There are various options for improving this in future versions. 
