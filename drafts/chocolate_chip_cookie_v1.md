---
layout: post
title:  "Every cookie is not the same"
date:   2016-11-03 16:05:19 +0100
categories: jekyll update
---

The [Good Eats](https://en.wikipedia.org/wiki/Good_Eats) series by chef [Alton Brown](https://en.wikipedia.org/wiki/Alton_Brown) are very entertaining as well as educational. In one episode called [Three chips for sister Marsha](http://www.foodnetwork.com/shows/good-eats/cl-series/three-chips-for-sister-marsha2.html), Alton Brown was bullied by his sister Marsha and had to make three chocolate chip cookies for her, each with distinct texture, called *The Chewy*, *The Puffy*, and *The Thin*. He managed to tune the consistency of a baked cookie by choosing different ingredients -- all-purpose flour or cake flour, butter or shortening, baking soda or baking powder, etc.

It's perhaps nothing special for a chef to make different kinds of cookies. What is special, howover, is that he has a proper explanation for each choice that he makes! Proper because they are not the kind of reasons that his grandmother did it that way or things like that. For example, because of the difference in compositions between baking powder and baking soda, baking powder would act already at low temperature. This way a cookie would have no time to spread before it will grow, which makes it puffy rather than thin! Btw, before that I didn't know that baking soda is a base while baking powder is neutral! Now to think of it, this is really one of the things that sent me down the path to chase science in home cooking.

But, there can be more than three types of chocolate chip cookies, right? I used to make a crumbly cookie for my daughter before she had tooth, for example. What about a cookie that's somewhat chewy and puffy (imagining croissant-cookie..)?

To find out how many cookies there are, I collected cookie recipes from the [foodnetwork](http://www.foodnetwork.com) site <sup>[1](#myfootnote1)</sup>, parsed them, and ran clustering analysis on the ingredient usage. Here is a summary of the results.

## Alton Brown's "Three Chips"

A bit more quantitative on the three recipes of Alton Brown: How are they different in ingredients exactly?

In the food recipe world, perhaps it's most straightforward to list all ingredients in a table side by side. I did exactly that, for the three cookie recipes from Alton Brown. See below. (source: [fan page](http://www.goodeatsfanpage.com/season3/cookie/cookietranscript.htm). Units are chosen such that the amount will be around 1 for each recipe, each ingredient.)


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


We see that for each category of ingredients, two or more alternative choices are used with different proportion per recipe. For example, as leavening agent, baking soda is used for The Chewy and The Thin, while baking powder is used for The puffy. I refer to the original episode for the explanations why baking powder and baking soda are different and how they can affect the texture of a baked cookie, etc. For even more details, [Harold McGee's](http://www.curiouscook.com/) [On Food and Cooking: The Science and Lore of the Kitchen](https://www.amazon.com/Food-Cooking-Science-Lore-Kitchen/dp/0684800012) is of course *the source* of all knowledge in a kitchen.

To summarize the choices for alternative ingredients, I plotted the ingredient usages of the three recipes on a 3D scatter plot. In this case it's sufficient to have only three dimensions, because the choices for fat (butter or shortening) or leavening (baking soda or baking powder) or flour (cake flour or all purpose flour) are linked to each other. Namely, The Puffy uses shortening, baking soda, and cake flour, while The Thin and The Chewy use butter, baking powder, and all-purpose (AP) flour <sup>[2](#myfootnote2)</sup>. Next to fat, leavening and flour, the usage of liquid (egg or milk) or sugar (white or brown) differentiates all three recipes. (The usage of chocolate chips is the same for all three recipes, which is therefore omitted in the figure.)

<img src="../pics/ab_phasediagram_pct_scatter3d_label.png" alt="Alton Brown Three Chips Phase Diagram" style="width: 70%;"/>

**todo remake phase diagram**

In this visualization, these three recipes do look very different. But where would the other cookie recipes be on this plot? Will they cluster around one of these three points, or spread evenly in between?

## Recipe data collection and preprocessing

### Download web data
To fill the "phase diagram" of cookies with more data points, I downloaded the ingredient list of all chocolate chip cookie recipes from the foodnetwork website. This "filtering" is done by using the search function of the site itself, and it finds 1020 recipes.

### Parse the ingredients
The ingredient description is further parsed into a table of the actual ingredient, the amount used, and the unit. For example, a description like this:

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

With ingredient, amount, and unit in separate columns, I can easily converted all units per ingredient to be the same across recipes. The resulting table in a wide format have 15 "features" contains ingredients information, and it looks like this for the three Alton Brown recipes mentioned in the beginning:


Title | cakeflour.cup | flour.cup | butter.cup | shortening.cup | brownsugar.cup | sugar.cup | milk.cup | water.cup | egg.ge | eggwhite.ge | eggyolk.ge | bakingpowder.tsp | bakingsoda.tsp | chocolate.cup | cocoa.cup
----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | -----
The Chewy | NA | 2.4 | 1 | NA | 1.144 | 0.286 | 0.125 | NA | 1 | NA | 1 | NA | 1 | 2.004 | NA
The Puffy | 2.25 | NA | NA | 1 | 1 | 0.75 | NA | NA | 2 | NA | NA | 1.5 | NA | 2 | NA
The Thin | NA | 2.25 | 1 | NA | 0.5 | 1 | 0.25 | NA | 1 | NA | NA | NA | 1 | 2 | NA

With "prior knowledge" from the Good Eats episode, I know that various ingredients are in fact of the same category:

* Flour category: include cake flour, all-purpose flour

* Fat category: include butter, shortening

* Sugar category, include white sugar, brown sugar

* Leavening agent category, include baking soda, baking powder

* Liquid category, include milk, water, egg, egg yolk, egg white

* Egg category, include egg, egg yolk, egg white

I also know that the choice of alternative ingredients per category is, by science, important for distinguishing different cookies. I can then create new variables that represent exactly that, e.g. the percentage of butter usage over total fat usage. This means that if the value is 0, then butter is not used and only shortening is used, while if the value is 100%, only butter is used. If neither is used, I label it as -1.

The new variables for the three Alton Brown recipes look like this:

Title | All-purpose / flour total | Butter / Fat | White sugar / sugar | Baking soda / leavening | Egg / Liquid | Yolk / egg
----- | ----- | ----- | ----- | ----- | ----- | -----
The Chewy | 1.0 | 1.0 | 0.2 | 1.0 | 0.7 | 0.3
The Puffy | 0.0 | 0.0 | 0.4 | 0.0 | 1.0 | 0.0
The Thin | 1.0 | 1.0 | 0.7 | 1.0 | 0.5 | 0.0

These new variables then replace one variable per category since that information becomes redundant.

The percentage of ingredient usage per category is in fact very informative. Figure below shows the six new variables for all 848 recipes. From the top two rows (percentage of AP flour over total flour and butter over total fat), it's already clear the the majority of the recipes prefer AP flour instead cake flour, prefer butter instead of shortening. The choice of white sugar or brown sugar (row 3) is more often mixed, with a perference to white sugar.

It's almost tempting to conclude that the Puffy is not very popular. But flour and butter is not the full story. Plus, (1) comparing to cake flour, all-purpose flour may be more often present in the pantry since it's multi-purpose, (2) and butter is tastier than shortening, (3) and most people believe shortening is not as healty, etc. So other ingredients still need to be considered.

**todo center image. make responsive figure size**

**todo replace geom_tile by heatmap?**

<img src="../pics/ingredients.pct.geom_tile.png" alt="All recipes alternative ingredients percentage" style="width: 90%;"/>

A large area remain gray -- those are the recipes that does not use any of the ingredient choices. I bluntly decides that a recipe is not a cookie recipe if it doesn't use flour (sorry for the 5 recipes that uses oatmeal instead) and removed them from the data. This left me with 488 recipes.

### Normalize to servings

A cookie recipe may yield 10 cookies or 20 cookies. The next step I normalize per recipe, all ingredients to the flour usage -- using flour amount to represent servings. (The extra percentage variables don't need to be normalized.)

After both transformations (percentage and normalization), I also removed 22 duplicated recipes <sup>[3](#myfootnote3)</sup>.


### Compare ingredient variable with percentage variable

Main reason for choosing the transformed data (percentage) <sup>[4](#myfootnote4)</sup> over the normalized original data is that, with the transformed data, only 5 PC's are needed to explain 95% of the variability, while the original data needs 9 PCs.

<img src="../pics/df.pctRatio.noflyer.cluster.var.cov.png" alt="cumulative variance proportion, percentage per ingredients category" style="width: 50%;"/>

<img src="../pics/df.scale.var.cov.png" alt="cumulative variance proportion, ingredients as is" style="width: 50%;"/>


### Outlier removal

### Meringue is not a cookie


To further clean up the data, I calculated Mahalanobis distance and marked 5% (24 recipes) of the recipes with the highest Mahalanobis distance as "outlier" recipes.

To visualize the outliers detected, I plotted the first two PCA components (see figure below). The two components could explain around 40% of the variation -- mainly caused by the outliers. Well, French Meringue, or Baked Alaska, or Florentines, or Cheesecakes, are definitely not cookies.

![](/pics/outlier.pc1.vs.pc2.png)

<img src="../pics/outlier.cor.pc1.vs.pc2.text.png" alt="Outlier recipes" style="width: 60%;"/>

### Distribution of the variables

After outlier removal, I plotted histogram for each ingredient (normalized). For an explanation of the meaning of each variable, see section [Percentage per ingredient category](### Percentage per ingredient category)

<img src="../pics/df.pctRatio.noflyer.hist.free.png" alt="Histogram all ingredients" style="width: 90%;"/>

**todo replace figure title**

The top row are percentage of alternative ingredients. The bottom row are the ingredients normalized to servings (represented by flour usage) per recipe, further scaled by range over all recipes. From the histogram, we see that:

* All-purpose flour, butter, and egg are preferred choices for flour type, fat type, and liquid, respectively. And these variables are almost binary.

* Most recipes won't use egg yolks next to whole egg.

* The choices for white sugar or brown sugar, and baking soda or baking powder, are more balanced -- possibly because these two types of ingredients are less important.

* Around 100 recipes don't even use any leavening agent at all -- reasonable for a cookie.

* Egg whites and cocoa are also not popular ingredients for a cookie.

* The proportion of butter, white sugar, egg, and chocolate over flour (servings)) has quite some spread in the distribution. The data is however skewed because they are all positive numbers.

## Clustering analysis

### Number of clusters

I use k-means to cluster the recipes. From the figure below, we see that with more than 5 clusters, the percentage of variations explained does not increase that much any more. So the number of clusters is selected to be 5.

<img src="../pics/kmeans_p.exp_vs_centers.png" alt="K-means wss vs number of clusters" style="width: 50%;"/>

### Visualizing the clustering

PCA is a good way to visualize these 5 clusters.

<img src="../pics/df.pctRatio.noflyer.cluster.cov.pc1.vs.pc2.png" alt="PCA visualization of the clusters" style="width: 60%;"/>

Alternatively, [t-SNE](https://cran.r-project.org/web/packages/Rtsne/index.html) does even a better job at visualizing all data.

<img src="../pics/rtsne_seed36perp50seedkmeans19_5cluster.png" alt="t-SNE visualization of the clusters" style="width: 60%;"/>

Both recipes The Chewy and The Thin ended up to be in the same cluster (cluster 5). But in both visualizations, this cluster 5 is quite close to cluster 4 (where recipe The Puffy is in) and cluster 1. In addition, cluster 2 and 3 are quite distinct from the rest.


### Cluster centers

To look at recipes of the center of the clusters may also reveal what differentiates different clusters.

<img src="../pics/kmeans.centers.barplot.png" alt="visualize cluster centers" style="width: 80%;"/>

From the barplot,

* Cluster 2 stands out because it doesn't use leavening agent such as baking soda or baking powder. It uses lots of egg white though -- which leavens!

* Cluster 3 mostly don't use egg (value = -1 for the egg/liquid and yolk/egg column) or leavening.

* Cluster 1 is very similar to the cookie clusters (4 and 5)!

* Cluster 4 and 5 are remarkably similar, even for those percentage variables which should have separated a puffy cookie from a chewy cookie.

To compare directly cluster center of group 4 and 5 with the three Alton Brown recipes, they are re-plotted side by side.

<img src="../pics/kmeans.centers.ab.barplot.png" alt="compare cluster centers with Alton Brown recipes" style="width: 80%;"/>

For most of the factors that Alton brown considered deterministic to the cookie texture,

* The baking soda usage matches expectation: the puffy cookie (in cluster 4) uses exclusively baking powder and no baking powder, and other recipes in the same cluster uses rather a mix of both leavening agents, but on average higher percentage of baking powder indeed.

* Other ingredients such as choice of flour, fat, or sugar, centers of cluster 4 and 5 appear very similar, which is quite unexpected.

I re-plotted the t-SNE visualization but coloring now recipes which use either shortening or cake flour -- these are the ingredients that *The Puffy* uses.

<img src="../pics/rtsne_vis_cakeflour_shortening.png" alt="recipes uses cake flour or shortening" style="width: 80%;"/>

Turns out that there are only very few recipes which would use cake flour or shortening, which explains what these factors are not found to differentiates clusters.

### Recipe titles

Running a quick word count through the recipe titles of each cluster. I plot then the top occurring words per cluster (only those words that appear in more than 10% of the words, are included in the plot.)

<img src="../pics/tm_wordcount_per_cluster_5groups.png" alt="word count per cluster" style="width: 80%;"/>

The recipe title confirms the observations on cluster centers:

* For group 1, 4, and 5, the key words "chocolate", "chip", and "cookie" are dominating.

* Cluster 2 and 3 contain more often words that are not obviously cookies.

* Cluster 3 has a high count on "dough". This is possible a cluster of cookie dough recipes. No matter they don't use eggs!


## Conclusions

The clustering method does a good job separating cookie recipes from non-cookie recipes.  perhaps adding this information could help the search engine of [foodnetwork](http://www.foodnetwork.com/) to spit out more relevant results -- unless they added the non-relevant recipes on purpose...

Otherwise all cookie recipes simply fill the space between (and beyond) the three Alton Brown recipes. Everyone who would publish a recipe apparently has a different preference!


## Footnotes

<a name="myfootnote1">1</a>: I choose to use foodnetwork because (1) Foodnetwork site collects recipes from mainly TV chefs. Perhaps there would be less repetition and higher chance that the recipes are tested. Thus the data quality may be better. (2) Foodnetwork site was also the place where the Good Eats series were originally broadcasted.

<a name="myfootnote2">2</a>: Okay The chewy uses bread flour instead of all purpose flour. Based on personal experience, I find the result of bread flour almost similar to all-purpose flour. In addition, for the following up analysis where I downloaded 800 cookie recipes for the web, there are only 2 recipes that use bread flour: [Alton Brown's The Chewy](http://www.foodnetwork.com/recipes/alton-brown/the-chewy-recipe.html/) and [Sin City Cookies from Giada De Laurentiis](http://www.foodnetwork.com/recipes/giada-de-laurentiis/sin-city-cookies.html). I therefore labelled both bread flour and all-purpose flour as all-purpose flour in this document.

<a name="myfootnote3">3</a>: For example, [Simple Chocolate Chip Cookies](http://www.foodnetwork.com/recipes/food-network-kitchens/simple-chocolate-chip-cookies.html), [Crispy-Cakey Chocolate Chip Cookies](http://www.foodnetwork.com/recipes/food-network-kitchens/crispy-cakey-chocolate-chip-cookies-recipe.html), and [Extra-Crispy Chocolate Chip Cookies](http://www.foodnetwork.com/recipes/food-network-kitchens/extra-crispy-chocolate-chip-cookies.html) are identical. Well, also [Mexican Chocolate Cookies by Alice Medrich](http://www.foodnetwork.com/recipes/mexican-chocolate-cookies-recipe.html) and [Cinnamon-Spiced Hot Chocolate Cookies by Aaron Sanchez](http://www.foodnetwork.com/recipes/cinnamon-spiced-hot-chocolate-cookies-recipe.html) are essentially the same..

<a name="myfootnote4">4</a>: Only 12 features for the transformed data (to percentage per category), versus 15 features for the original data, because I removed features that I consider not important. These are: water usage per unit flour (only very few recipes use water, and water information is used in calculate egg over total liquid percentage); flour usage per unit flour total (same as flour percentage); and baking soda usage per unit flour (I think it's not that important..)

**todo, lookup what likelyhood means**





You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
