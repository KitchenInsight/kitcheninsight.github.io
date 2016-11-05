---
layout: post
title:  "Every cookie is not the same"
date:   2016-11-03 16:05:19 +0100
categories: jekyll update
---

### Three chips for sister Marsha

The [Good Eats](https://en.wikipedia.org/wiki/Good_Eats) series by chef [Alton Brown](https://en.wikipedia.org/wiki/Alton_Brown) are very entertaining as well as educational. In one episode called [Three chips for sister Marsha](http://www.foodnetwork.com/shows/good-eats/cl-series/three-chips-for-sister-marsha2.html), Alton Brown was bullied by his sister Marsha and had to make three chocolate chip cookies for her, each with distinct texture, called *The Chewy*, *The Puffy*, and *The Thin*. He managed to tune the consistency of a baked cookie by choosing different ingredients -- all-purpose flour or cake flour, butter or shortening, baking soda or baking powder, etc.

It's perhaps nothing special for a chef to make different kinds of cookies. What is special, howover, is that he has a proper explanation for each choice that he makes! Proper because they are not the kind of reasons that his grandmother did it that way or things like that. For example, because of the difference in compositions between baking powder and baking soda, baking powder would act already at low temperature. This way a cookie would have no time to spread before it will grow, which makes it puffy rather than thin! Btw, before that I didn't know that baking soda is a base while baking powder is neutral! Now to think of it, this is really one of the things that sent me down the path to chase science in home cooking.

But, there can be more than three types of chocolate chip cookies, right? I used to make a *crumbly* cookie for my daughter before she had tooth, for example. What about a cookie that's somewhat chewy and puffy (imagining croissant-cookie..)?

To find out how many cookies there are, I collected cookie recipes from the [foodnetwork](http://www.foodnetwork.com) site, parsed them. (For the details of data extraction and transformation, see [preprocessing of cookie data](todo add link).) This gives me more than 400 cookie recipes, where I ran clustering analysis on the ingredient usage. And here is a summary of the results.

### Meringue is not a cookie

Now, a cookie is simplified to 12 numbers here, each representing the amount of flour, butter, egg, sugar, leavening etc, used in a recipe. PCA can be a great tool to visualize such a 12-dimensional dataset. Below is a scatter plot of the 400 cookie recipes, represented by the first two principle components. A principle component is just a linear combination of the previously mentioned 12 number sets, chosen in a smart way such that the recipes can have a large spread on this axis. The large spread helps visualization!

![](/pics/outlier_cor_pc1_vs_pc2.png)

The recipes labelled by the red points appear quite separated from the rest of the black points. A closer look at the title of these red recipes (recipe title is shown on the graph) tells that they are French Meringue, or Baked Alaska, or Florentines, or Cheesecakes, and they are definitely not cookies.

This is interesting -- why would the search engine of foodnetwork give me also these recipes when I search only for chocolate chip cookies?

Anyway, to clean up the data, I calculated Mahalanobis distance and marked 5% (24 recipes) of the recipes with the highest Mahalanobis distance as "outlier" recipes ("non-cookie" recipes) and removed them from further analysis. These are the 24 red points on the graph above. Some of these 24 red points (non-cookie) seem overlap with the black points (cookie). That is because the first two principle components explain only 40 % of the variations. These overlapping points would show up as separated if I use more components.

### How many types of cookies?

Now that I have a clean data set, finally I got to see how many clusters there are.

I use k-means to cluster the recipes. In order to how many groups there are, I plotted the BIC value for different number of clusters.

![](/pics/kmeans_BIC_centers.png)

[BIC](https://en.wikipedia.org/wiki/Bayesian_information_criterion) stands for Bayesian information criterion, and is basically a sum of two competing terms. One is the total variation within each cluster. This variation of course decrease with increasing number of clusters, and is zero when the number of clusters is equal to the number of samples. A second term penalizes the increase in the number of clusters. The optimal number of groups minimizes the BIC value.

In the cookie case, the minimum of the BIC curve suggests that there are four types of recipes.

PCA is a good way to visualize these 4 groups. Below I plotted each recipe on the axis of the first two principle components (now without the outliers).

![](/pics/rec_noflyer_cluster_cov_pc1_vs_pc2.png)

From this visualization, group 4 is clearly separated from the rest of the recipes. Otherwise Alton Brown's *chewy* and *Thin* recipe are in the same group with the largest number of recipes, while the *puffy* recipe is grouped with a different set of recipes. These groups are overlapping heavily on this visualization, probably because these first two principle components explain only 60 % of the variation.

Alternatively, [t-SNE](https://cran.r-project.org/web/packages/Rtsne/index.html) does even a better job at visualizing all data. Now each group are clearly separated.

![](/pics/rec_noflyer_cluster_rtsne.png)


### Different types of chocolate cookies on the web

To find out what are the differences between different cookie groups, I plotted the key ingredients of each group -- an average recipe per group that is.

And next to it, I also plotted per group, the most frequently occurring words in the recipe title.

![](/pics/ingredients_centroids_bar_wrap.png)

![](/pics/ingredients_tm_wordcount.png)

Group 1 turns out to be a standard cookie group -- using flour, butter, leavening, and whole eggs. The titles of this group are also dominated by "chocolate", "chip", and "cookie". Alton Brown's *chewy* and *thin* cookies also fall into this group.

Group 2 is almost the same, but often doesn't use leavening at all. Sounds ok. I can totally imagine a cookie without leavening. Looking at the word count in this group, apparently without leavening then it's only a "chocolate ~~chip~~ cookie".

These two are the real cookie groups then.

Group 3 however, almost doesn't use fat (labelled -1 in the data, see todo add link to preprocessing). Here the titles tend to use words like *biscotti*, *gluten-free*. So these are the *healthy biscuits*. How did Alton Brown's *Puffy* cookie end up in this group is surprising <sup>[1](#myfootnote1)</sup>.

The last group uses neither eggs or leavening. These turn out to be *cookie dough*, *shortbread cookie* recipes.

### The cookie proposition

So the different groups are not different types of chocolate chip cookies, but rather cookie or non-cookie. Fair enough, a biscotti uses definitely different ingredient proportions from a chocolate chip cookie.

For the cookies, it seems that they don't like to be grouped. All cookie recipes simply fill the space between (and beyond) the three Alton Brown recipes. Apparently, everyone who would publish a recipe apparently has a different preference!

Now I know that if I ask for cookie recipes to a search engine, it will give me a list cookies, healthy options, cookie dough, Cheesecakes, and baked Alaska. Perhaps for the next generation of recipe search algorithm, it's an idea to rank the recipes also using their ingredient usage -- I do promise a more accurate search result!


---------------------

<a name="myfootnote1">1</a>: *The Puffy is different from the other cookies mainly because it uses cake flour and shortening instead of all-purpose flour and butter. For all the cookie recipes in my dataset, only 17 out of 466 recipes are using cake flour or shortening. These parameters thus became less differentiating factors. However, this is probably not because puffy cookies are not popular. Other factors play a role as well. For example, many people consider shortening to be unhealthy. And in someone's pantry, cake flour is also available less often than all-purpose flour.*
