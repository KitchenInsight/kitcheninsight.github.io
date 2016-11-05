---
layout: post
title:  "Every cookie is not the same, data pre-processing"
date:   2016-11-02 20:16:19 +0100
categories: jekyll update
---

[Alton Brown](https://en.wikipedia.org/wiki/Alton_Brown) has made an episode for [Good Eats](https://en.wikipedia.org/wiki/Good_Eats), called [Three chips for sister Marsha](http://www.foodnetwork.com/shows/good-eats/cl-series/three-chips-for-sister-marsha2.html). In this episode he explained how to make different types of chocolate chip cookies. The difference is mainly in the choice of ingredients and their proportions.

I was curious whether various cookie recipes on the web would be similar or different from these three distinct recipes of Alton Brown. So I collected cookie recipes from the [foodnetwork](http://www.foodnetwork.com) site <sup>[1](#myfootnote1)</sup>, parsed them, and ran clustering analysis on the ingredient usage. See my [post](https://kitcheninsight.github.io/jekyll/update/2016/11/03/every-cookie-is-not-the-same.html) for the results. Here are the details of data extraction and transformation.

### Alton Brown's recipes and how to transform data

Before I get to the data extraction part, first a summary of the cookie recipes of Alton Brown. This knowledge will become important for later deciding on how to transform the data.

All three recipes use only 10 different ingredients in approximately 6 categories. The exact amount is summarized in the table below. (source: [fan page](http://www.goodeatsfanpage.com/season3/cookie/cookietranscript.htm). Units are chosen such that the amount will be around 1 for each recipe, each ingredient.)

| Ingredients     | The Chewy              | The Puffy             | The Thin                     |
|-----------------|------------------------|-----------------------|------------------------------|
| Flour           | Bread flour 2 1/4 cups | Cake flour 2 1/4 cups | All purpose flour 2 1/4 cups |
| Fat             | Butter 1 cup           | Shortening 1 cup      | Butter 1 cup                 |
| Leavening       | Baking soda 1 tsp      | Baking powder 1.5 tsp | Baking soda 1 tsp            |
| Egg             | 1 whole 1 yolk         | 2 whole               | 1 whole                      |
| Whole milk      | 1 oz                   | 0                     | 2 oz                         |
| White sugar     | 1/4 cup                | 3/4 cup               | 1 cup                        |
| Brown sugar     | 1 1/4 cups             | 1 cup                 | 1/2 cup                      |
| Chocolate chips | 2 cups                 | 2 cups                | 2 cups                       |

And the 6 categories are:

* **Flour category**: include cake flour, all-purpose flour

* **Fat category**: include butter, shortening

* **Sugar category**, include white sugar, brown sugar

* **Leavening agent category**, include baking soda, baking powder

* **Liquid category**, include milk, water, egg, egg yolk, egg white

* **Chocolate**

All three cookie recipes use all 6 categories of ingredients. Alton Brown's method is to choose alternative ingredients for different recipes, and change their proportion. For example, as leavening agent, baking soda is used for The Chewy and The Thin, while baking powder is used for The puffy. I refer to the original episode for the explanations why baking powder and baking soda are different and how they can affect the texture of a baked cookie, etc. For even more details, [Harold McGee's](http://www.curiouscook.com/) [On Food and Cooking: The Science and Lore of the Kitchen](https://www.amazon.com/Food-Cooking-Science-Lore-Kitchen/dp/0684800012) is of course *the source* of all knowledge in a kitchen.

Since the proportion is important for differentiating recipes, the same recipes can be transformed into the proportions of each ingredient within that category. For example, the fat category can be butter or shortening. If a recipe uses 1 cup butter but no shortening, the proportion of butter to fat would be 100 %, and shortening to fat is 0 %. After applying this transformation, the three Alton Brown recipes become:

| Ingredients     | The Chewy   | The Puffy             | The Thin                     |
|-----------------|-------------|-----------------------|------------------------------|
| AP flour / Flour | 1          | 0                     | 1                            |
| Butter / Fat    | 1           | 0      | 1                 |
| Baking soda / Leavening       | 1      | 0    | 1            |
| Egg/Liquid      | 0.7         | 1               | 0.5                      |
| White sugar / Sugar    | 0.2        | 0.4               | 0.7                        |
| Egg Yolk / Egg      | 0.3         | 0               | 0                      |

This table contains similar<sup>[2](#myfootnote2)</sup> information as the previous table, but now the differences are much more obvious. The choices for fat or leavening or flour are linked to each other. Namely, The Puffy uses shortening, baking soda, and cake flour, while The Thin and The Chewy use butter, baking powder, and all-purpose (AP) flour<sup>[3](#myfootnote3)</sup>. Next to fat, leavening and flour, the usage of liquid (egg or milk) or sugar (white or brown) differentiates all three recipes. (The usage of chocolate chips is the same for all three recipes, which is therefore omitted in the table.)

The same transformation will be applied to the entire data set.

### Download recipe data from web
[foodnetwork](http://www.foodnetwork.com) site uses [h-recipe](http://microformats.org/wiki/hrecipe) format, which is well structured and is relatively simple to parse. I downloaded the ingredient list of all chocolate chip cookie recipes from the foodnetwork website. This "filtering" is done by using the search function of the site itself, and it finds 1020 recipes.

### Parse the ingredients
The ingredient descriptions are further parsed into a table of the actual ingredient, the amount used, and the unit. For example, a description like this:

> 2 1/2 sticks unsalted butter, at room temperature

is then parsed into three variables:

Ingredient | Amount | Unit
-----------|--------|--------
butter     |2.5     |stick

I then removed all recipes where the key ingredients miss either amount or unit. For example, one ingredient description is:

> 1/2 all-purpose flour

In this example, the author clearly made a typo and forgot to mention 1/2 *what* of all-purpose flour. Such entries are then removed.

After the clean up there are 848 recipes left.


### Percentage per ingredient category

With ingredient, amount, and unit in separate columns, all units per ingredient can be easily converted to the same across recipes. The resulting table in a wide format have 15 "features" contains ingredients usage.

Since the relative proportions of alternative ingredients per category is, by science, important for distinguishing different cookies, I then create new variables that represent exactly that, e.g. the percentage of butter usage over total fat usage. This means that if the value is 0, then butter is not used and only shortening is used, while if the value is 100%, only butter is used. If neither is used, I label it as -1.

These new variables then replace one variable per category since that information becomes redundant.

The percentage of ingredient usage per category is in fact very informative. Figure below shows the six new variables for all 848 recipes. From the top two rows (percentage of AP flour over total flour and butter over total fat), it's already clear the the majority of the recipes prefer AP flour instead cake flour, prefer butter instead of shortening. The choice of white sugar or brown sugar (row 3) is more often mixed, with a preference to white sugar.

It's almost tempting to conclude that the Puffy is not very popular. But flour and butter is not the full story. Plus, (1) comparing to cake flour, all-purpose flour may be more often present in the pantry since it's multi-purpose, (2) and butter is tastier than shortening, (3) and most people believe shortening is not as healty, etc. So other ingredients still need to be considered.

![](/pics/20161102_ecns_preprocessing/ingredients_pct_geom_tile.png)

A large area remain gray -- those are the recipes that does not use any of the ingredient choices. I bluntly decides that a recipe is not a cookie recipe if it doesn't use flour (sorry for the 5 recipes that uses oatmeal instead) and removed them from the data. This left me with 488 recipes.

### Normalize to servings

A cookie recipe may yield 10 cookies or 20 cookies. The next step I normalize per recipe, all ingredients to the flour usage -- using flour amount to represent servings. (The extra percentage variables don't need to be normalized.)

After both transformations (percentage and normalization), I also removed 22 duplicated recipes <sup>[4](#myfootnote4)</sup>.

### Compare ingredient variable with percentage variable

Main reason for choosing the transformed data (percentage) <sup>[5](#myfootnote5)</sup> over the normalized original data is that, with the transformed data, only 5 PC's are needed to explain 95% of the variability, while the original data needs 9 PCs. (See figures below.)

![](/pics/20161102_ecns_preprocessing/ingredients_raw_pca_cov_var.png)

![](/pics/20161102_ecns_preprocessing/ingredients_pct_pca_cov_var.png)

### Distribution of the variables

After data transformation, I plotted histogram for each ingredient (normalized). The top row are percentage of alternative ingredients. The bottom row are the ingredients normalized to servings (represented by flour usage) per recipe, further scaled by range over all recipes.

![](/pics/20161102_ecns_preprocessing/ingredients_hist.png)

From the histogram, we see that:

* All-purpose flour, butter, and egg are preferred choices for flour type, fat type, and liquid, respectively. And these variables are almost binary.

* Most recipes won't use egg yolks next to whole egg.

* The choices for white sugar or brown sugar, and baking soda or baking powder, are more balanced -- possibly because these two types of ingredients are less important.

* Around 100 recipes don't even use any leavening agent at all -- reasonable for a cookie.

* Egg whites and cocoa are also not popular ingredients for a cookie.

* The proportion of butter, white sugar, egg, and chocolate over flour (servings)) has quite some spread in the distribution. The data is however skewed because they are all positive numbers.

### Patterns in cookie recipes?

The goal of the above exercise is to prepare data for investigating whether there are patterns in web data of cookie recipes. See [next post](https://kitcheninsight.github.io/jekyll/update/2016/11/03/every-cookie-is-not-the-same.html) for the results!


------------------

### Footnotes

<a name="myfootnote1">1</a>: I choose to use foodnetwork because (1) Foodnetwork site collects recipes from mainly TV chefs. Perhaps there would be less repetition and higher chance that the recipes are tested. Thus the data quality may be better. (2) Foodnetwork site was also the place where the Good Eats series were originally broadcasted.

<a name="myfootnote2">2</a>: Similar but not the same, because here the across-category proportion is not listed. For example, how much butter per cup of flour. In actual analysis, this information is included.

<a name="myfootnote3">3</a>: Okay The chewy uses bread flour instead of all purpose flour. Based on personal experience, I find the result of bread flour almost similar to all-purpose flour. In addition, for the following up analysis where I downloaded 800 cookie recipes for the web, there are only 2 recipes that use bread flour: [Alton Brown's The Chewy](http://www.foodnetwork.com/recipes/alton-brown/the-chewy-recipe.html/) and [Sin City Cookies from Giada De Laurentiis](http://www.foodnetwork.com/recipes/giada-de-laurentiis/sin-city-cookies.html). I therefore labelled both bread flour and all-purpose flour as all-purpose flour in this document.

<a name="myfootnote4">4</a>: For example, [Simple Chocolate Chip Cookies](http://www.foodnetwork.com/recipes/food-network-kitchens/simple-chocolate-chip-cookies.html), [Crispy-Cakey Chocolate Chip Cookies](http://www.foodnetwork.com/recipes/food-network-kitchens/crispy-cakey-chocolate-chip-cookies-recipe.html), and [Extra-Crispy Chocolate Chip Cookies](http://www.foodnetwork.com/recipes/food-network-kitchens/extra-crispy-chocolate-chip-cookies.html) are identical. Well, also [Mexican Chocolate Cookies by Alice Medrich](http://www.foodnetwork.com/recipes/mexican-chocolate-cookies-recipe.html) and [Cinnamon-Spiced Hot Chocolate Cookies by Aaron Sanchez](http://www.foodnetwork.com/recipes/cinnamon-spiced-hot-chocolate-cookies-recipe.html) are essentially the same..

<a name="myfootnote5">5</a>: Only 12 features for the transformed data (to percentage per category), versus 15 features for the original data, because I removed features that I consider not important. These are: water usage per unit flour (only very few recipes use water, and water information is used in calculate egg over total liquid percentage); flour usage per unit flour total (same as flour percentage); and baking soda usage per unit flour (I think it's not that important..)
