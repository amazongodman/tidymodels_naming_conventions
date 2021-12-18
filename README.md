
# Suggestion for object(variable) naming conventions for tidymodels.







# Object naming conventions in R


There is sometimes debate about object naming conventions in programming languages.

https://en.wikipedia.org/wiki/Naming_convention_(programming)

You may think that it is more fun to give your child a name you like.
To be honest, I too find it a hassle to be forced to follow a naming convention and give a name.
But the naming convention still exists.
The reasons for this are to reduce the stress of writing code and to make it easier to understand when re-reading it later.
If the name of the object does not correspond to its content, you will have to re-read all the code to understand the relationship between the object and its content.
If you are not the only one who might read the code, it is confusing to give it any name you like.
The object name and content must be linked, like your email address and yourself.

Some naming conventions are given in the style guides published by google and  tidyverse.

https://google.github.io/styleguide/Rguide.html


https://style.tidyverse.org/





Some of the rules are

- Variable names should be concise
- Function names are verbs
- Variable names are nouns
- Variable names should not contain a dot symbol

and so on.



Also, different style guides may have different recommended rules.


- Use BigCamelCase for variable names [google].
- Use lowercase letters and underscores(_) in variable names [tidyverse].

These are just rules, there is no right answer.
Although not listed here, some old R users may have followed the rule of using the dot symbol(.) in variable names.

[What is the meaning of the "." (dot) in R?](https://stats.stackexchange.com/questions/10712/what-is-the-meaning-of-the-dot-in-r)


And it is mentioned in the style guide that "**it is very difficult to follow the rule**".


> Generally, variable names should be nouns and function names should be verbs. Strive for names that are concise and meaningful (this is not easy!).
-- The tidyverse style guide [2.1 Object names]

# Rules(principles) for tidymodels


tidymodels and tidyverse, is a package led by Rstudio.
The members working on it are all familiar with the tidyverse and R rules.

The following [Tidy Modeling with R: a.k.a. TMwR](https://www.tmwr.org/) also contains a bit about the rules and philosophy.

https://www.tmwr.org/tidyverse.html#principles

However, when you read the code in TMWR, you may find that the objects are named differently even though their contents are the same.

Therefore, I would like to organize the relationship between names and contents in TMwR and suggest naming conventions to help you when you feel like writing tidy code.


# object name and contents table


The names of the objects were written in a clear way using suffixes.
I mainly checked this by looking at TMWR, but I also referred to blogs of people who wrote tidymodels code.
The names were given in the following relationships.



|names|objects||names|objects|
|---|---|---|---|---|
|*_split <br> *_spl|initial_split()||*_train|training()|
|spl|smooth.spline()||*_test|testing()|
|resample|mc_cv()||*_mod <br> *_model <br> *_spec|parsnip models|
|*_folds|vfold_cv()||*_rec <br> *_recipe|recipes objects|
|rs|bootstraps()||*_rec_trained|prep()|
|*_val_set|validation_split()||*_processed|bake()|
|*_metrics|metric_set()||*_wfl <br> *_wflow|workflows objects|
|ctrl <br> keep_pred <br> ctrl_**AAA**|control_**AAA**()||*_param|parameters()|
|*_grid|grid_**AAA**||*_tune|tune_grid()|
|grid_**AAA**|tune_race_**AAA**||*_fit|fit()|
|*_res|collect_metrics() <br> collect_predictions() <br> fit_resample() <br> last_fit() <br> **and mode...**||||



I've been reading some tidymodels code that some people have written, and I've been thinking



- Cross-validation has variations in the names.
- The role of res is unclear.
- The use of variables for detailed settings

I think these are the reasons why named objects are so complicated.



So I'll try to simplify them by dividing them into certain groups.
In order to make the following table easier to understand, I will assume that you are using data ames.




|names[suggestion]|objects|
|---|---|
|ames|data(ames)|
|ames_split|initial_split()|
|ames_train|training()|
|ames_test|testing()|
|ames_cv|mc_cv() <br> vfold_cv() <br> bootstraps() <br> validation_split()|
|ames_rec|recipes objects <br> finalize_recipe()|
|ames_spec|parsnip models <br> finalize_model()|
|ames_wflow|workflows objects <br> finalize_workflow() <br> update()|
|ames_fit|fit() <br> fit_members()|
|ames_cv_res|fit_resample()|
|ames_grid|grid_*() <br> crossing()|
|ames_tune_res|tune_*()|
|ames_last_res|last_fit()|
|*_param|select_best() <br> parameters()|
|*_perf|yardstick result <br> collect_metrics() <br> rank_results()|
|Don't create|control_*() <br> prep() <br> bake() <br> metric_set()|




Here is an explanation of why the table above is the way it is.

## 1. In parsnip, the idea is to separate the data from the specification, so we use *_spec.
I used the term "spec" instead of "model".

## 2. prep and bake are temporary variables, but should be executed when used.
while modeling with tidymodels, the data created by prep and bake was used for checking the data rather than creating models, so I did not set a naming convention. If you are going to use them for model input, it is recommended to use _train or _test.


## 3. control_* should be written directly to options.
you should type control_*() directly in the "control argument". The sentence of code will be longer, but since there are multiple types of control_*(), there is no need to create complicated variables.


## 4. Do not create objects from metric_set, write it directly to options. 
the reason is same in 3: there are no types of metric_set(). Selecting more metrics will make your code longer, but I don't think it's a big problem, so please don't create variables and write them into the "argument".


## 5. Use *_cv for objects that are for cross-validation purposes, including bootstraps.
Although bootstraps can be used in a variety of ways, for the purpose of machine learning, let's use it as cross-validation data. Although neither bootstraps nor validation_split has cv in the name of the function, they are both "rset class" and have in common that they can be entered into fit_resample, etc. As a side note, initial_split is an "rsplit class".

## 6. Use *_res for results using cross-validated data.
I defined how to use *_res. tune_* and fit_resample returns are the result of fit() with cross-validation data. The results of the last_fit() has "last_fit class" and can be used with collect_metrics()."resample_results class" and "tune_results class" can use collect_metrics() too. In addition, it can be used as input for stacks::add_candidates() and tidyposterior::perf_mod().


## 7. Use *_perf if you are checking the performance.
I have differentiated the results measured using metrics that have been considered _res from "results" by using the name "performance".

# Finally.

Please let me know if there are any omissions.
Since tidymodels may change the function, this is not a decision but needs to be modified.

# Update history

2021-12-19: First version submitted.







