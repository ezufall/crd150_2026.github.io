---
title: 'Missing Data in PolicyMap'
subtitle: <font size="4">CRD 150 - Quantitative Methods in Community Research</font>
author: Professor Noli Brazil
output: 
  html_document:
    toc: true
    toc_depth: 3
    toc_float: true
    theme: cosmo
    code_folding: show
---




<style>
p.comment {
background-color: #DBDBDB;
padding: 10px;
border: 1px solid black;
margin-left: 25px;
border-radius: 5px;
font-style: normal;
}

.figure {
   margin-top: 20px;
   margin-bottom: 20px;
}

h1.title {
  font-weight: bold;
  font-family: Arial;  
}

h2.title {
  font-family: Arial;  
}

</style>


<style type="text/css">
#TOC {
  font-size: 13px;
  font-family: Arial;
}
</style>


\




This guide provides step-by-step instructions for downloading data from [PolicyMap](https://ucdavis.policymap.com/newmaps#/). PolicyMap is a fully web-based online data and mapping application that gives you access to over 15,000 indicators related to demographics, housing, crime, mortgages, health, jobs and more. Data are available at all common geographies (address, block group, census tract, zip code, county, city, state, Metropolitan area) as well as unique geographies like school districts and political boundaries. 

In this guide, we will download PolicyMap census tract data for the City of Oakland. We will download median housing value.   UC Davis provides full access to all PolicyMap tools for staff, students, and faculty. We will download data from the UCD PolicyMap portal and clean up the files in R.   Much of this we already covered in [Lab 3](https://crd150.github.io/lab3.html).  

<div style="margin-bottom:25px;">
</div>
## **Installing and Loading Packages**
\

We'll be using the package **VIM** in this guide. Install it if you already haven't.


``` r
install.packages("VIM")
```

Then load it and the other packages we will be using in this guide.


``` r
library(tidyverse)
library(tidycensus)
library(VIM)
```


<div style="margin-bottom:25px;">
</div>
##  **Download data**
\


1. Navigate to the UC Davis [PolicyMap portal](https://ucdavis.policymap.com/maps).  You should see a webpage that looks like the figure below.  Note the UC Davis logo on the top right.  Go Aggies!

<center>
![](/Users/noli/Documents/UCD/teaching/CRD150/Lab/crd150.github.io/pmpage.png)

</center>


2. You should see a *Location* search bar somewhere near the top of the page.  Type in "Oakland, CA" in the search bar and *Oakland, CA (City)* should pop up below - select it.

<center>
![](/Users/noli/Documents/UCD/teaching/CRD150/Lab/crd150.github.io/typeoakland.png)

</center>

You should get a map that highlights Oakland's boundaries.

<center>
![](/Users/noli/Documents/UCD/teaching/CRD150/Lab/crd150.github.io/oaklandmap1.png)

</center>

3. The map does not show any data.  Let's add Median Housing Value. Click on the *Housing* tab, followed by *Median Value* under *Home Values*.

<center>
![](/Users/noli/Documents/UCD/teaching/CRD150/Lab/crd150.github.io/medval.png)

</center>

Now your map should look like the following

<center>
![](/Users/noli/Documents/UCD/teaching/CRD150/Lab/crd150.github.io/medvalmap.png)

</center>

4. Notice in the legend window you can change various aspects of the variable, including the year

<center>
![](/Users/noli/Documents/UCD/teaching/CRD150/Lab/crd150.github.io/pmyear.png)

</center>

the data type

<center>
![](/Users/noli/Documents/UCD/teaching/CRD150/Lab/crd150.github.io/pmdata.png)

</center>

and the geographic level.  

<center>
![](/Users/noli/Documents/UCD/teaching/CRD150/Lab/crd150.github.io/pmgeo.png)

</center>

**Be very careful about the geographic level. Users often do not realize that they are downloading an incorrect geographic scale. For example, you want to download census tract, but instead you download the Zip code. If you find that the geographic scale is greyed out under the pull down menu, Zoom in and out of the map.**


Leave the defaults (Year: 2019-2023, Variable: Median dollars, and Shaded by: Census Tract, 2022).  

5. Let's download these data.  At the top right of the site, click on the download icon ![](/Users/noli/Documents/UCD/teaching/CRD150/Lab/crd150.github.io/download.png).  


6. A window should pop up. The first screen on the left (Select Data) tells you what data to download - it should be "Estimated median value of owner-occupied home, between 2019-2023" under Layer, with "2019-2023" under Year and "Median dollars" under Variable already selected.

<center>
![](/Users/noli/Documents/UCD/teaching/CRD150/Lab/crd150.github.io/pmfirst.png)

</center>


7. The next section (Select Location) asks you to select a location.  It should be *City: Oakland, CA* and *Census Tract, 2022* for "Get values for geography".    


<center>
![](/Users/noli/Documents/UCD/teaching/CRD150/Lab/crd150.github.io/pmsecond.png)

</center>


8. The last section asks you to confirm the download. Select *Machine-readable data file (csv) + data specs file (csv)* under "Data Format". Select *Download via browser* under "Delivery Method". Then click on the Download button.

<center>
![](/Users/noli/Documents/UCD/teaching/CRD150/Lab/crd150.github.io/pmthird.png)

</center>

9. After a minute or two, a screen like below (on a Mac laptop) should pop up at the top right corner of your screen (the file name will differ from yours).  You can download this file again until the date given.

<center>
![](/Users/noli/Documents/UCD/teaching/CRD150/Lab/crd150.github.io/downloadcsv.png)

</center>



10. PolicyMap allows you to download only one variable at the time.  So, you'll need to go through the above steps again to get other variables. 


**After you've downloaded a csv file whether through PolicyMap or another source, do not open it up in Excel to manipulate or alter the data in any way. All of your data wrangling should be done in R**

<div style="margin-bottom:25px;">
</div>
## **Data Wrangling in R**
\

Bring in the data using `read_csv()`. 


``` r
pm.file <- read_csv("YOUR FILE NAME HERE")
```



Make sure to take a look at the data.


``` r
glimpse(pm.file)
```

```
## Rows: 116
## Columns: 10
## $ GeoID_Description <chr> "Census Tract", "Census Tract", "Census Tract", "Cen…
## $ GeoID_Name        <chr> "06001400100", "06001400200", "06001400300", "060014…
## $ SitsinState       <chr> "CA", "CA", "CA", "CA", "CA", "CA", "CA", "CA", "CA"…
## $ GeoID             <chr> "06001400100", "06001400200", "06001400300", "060014…
## $ GeoID_Formatted   <chr> "=\"06001400100\"", "=\"06001400200\"", "=\"06001400…
## $ mhv               <chr> "1802100", "1930000", "1850900", "1398400", "1313500…
## $ TimeFrame         <chr> "2019-2023", "2019-2023", "2019-2023", "2019-2023", …
## $ GeoVintage        <dbl> 2022, 2022, 2022, 2022, 2022, 2022, 2022, 2022, 2022…
## $ Source            <chr> "Census", "Census", "Census", "Census", "Census", "C…
## $ Location          <chr> "Oakland (City, 2020)", "Oakland (City, 2020)", "Oak…
```

Let's keep the necessary variables: *GeoID* and *mhv*. *GeoID* is the tract GEOID.


``` r
pm.file <- pm.file %>%
          select(GeoID, mhv)
```


<div style="margin-bottom:25px;">
</div>
### **Merging with Census API data**
\

Let's bring in some census data from the Census API.  We covered how to use `get_acs()` from the **tidycensus** package in [Lab 3](https://crd150.github.io/lab3.html). Make sure the ACS years match up between PolicyMap and Census API data. Census tract boundaries changed in 2020, which means that 2016-2020 tract data will not completely merge with ACS data between 2010 and 2019.


``` r
ca <- get_acs(geography = "tract", 
              year = 2023,
              variables = c(tpopr = "B03002_001", 
                            nhwhite = "B03002_003", nhblk = "B03002_004", 
                            nhasn = "B03002_006", hisp = "B03002_012"), 
              state = "CA",
              survey = "acs5",
              output = "wide")

ca <- ca %>%
        select(GEOID, tpoprE, nhwhiteE, nhblkE, nhasnE, hispE)
```

Merge in *ca* into *pm.file*.  The linking variable is *GeoID* in *pm.file* and *GEOID* in *ca*.


``` r
pm.file <- pm.file %>%
              left_join(ca, by = c("GeoID" = "GEOID"))
```

Take a look at the data to make sure the merge went well.


``` r
glimpse(pm.file)
```

```
## Rows: 116
## Columns: 7
## $ GeoID    <chr> "06001400100", "06001400200", "06001400300", "06001400400", "…
## $ mhv      <chr> "1802100", "1930000", "1850900", "1398400", "1313500", "12158…
## $ tpoprE   <dbl> 3094, 2093, 5727, 4395, 3822, 1957, 4404, 4583, 2752, 6529, 5…
## $ nhwhiteE <dbl> 2107, 1408, 3365, 2645, 1696, 801, 2230, 2441, 959, 1985, 239…
## $ nhblkE   <dbl> 137, 43, 524, 433, 911, 544, 1054, 371, 759, 2448, 1003, 207,…
## $ nhasnE   <dbl> 462, 256, 609, 422, 306, 101, 278, 690, 211, 398, 825, 473, 7…
## $ hispE    <dbl> 200, 196, 497, 604, 557, 198, 453, 596, 509, 896, 774, 355, 3…
```

<div style="margin-bottom:25px;">
</div>
### **Dealing with missing values**
\

Missing values are a part of a social scientist's life.  You can't avoid them.  

You will notice that PolicyMap designates missing values as *N/A*.  

<center>
![](/Users/noli/Documents/UCD/teaching/CRD150/Lab/crd150.github.io/na.png)

</center>


R designates missing as *NA*.  Therefore, R reads *N/A* as a character. This means that R does not recognize *mhv* as a numeric but a character.


``` r
class(pm.file$mhv)
```

```
## [1] "character"
```

We need to replace *N/A* with *NA* and then convert it to numeric


``` r
pm.file <- pm.file %>%
              mutate(mhv = as.numeric(ifelse(mhv == "N/A", "NA", mhv)))
```

The code `ifelse(medval == "N/A", "NA", medval)` says that if the variable *mhv* equals "N/A", replace it to "NA", else keep its original value stored in *mhv*.  `as.numeric()` converts *mhv* to a numeric.


``` r
class(pm.file$mhv)
```

```
## [1] "numeric"
```

The next step is to determine what percentage of your cases are missing data.  The best function for doing this in R is `aggr()`, which is in the **VIM** package. Run the `aggr()` function as follows


``` r
summary(aggr(pm.file))
```

![](policymap_files/figure-html/unnamed-chunk-13-1.png)<!-- -->

```
## 
##  Missings per variable: 
##  Variable Count
##     GeoID     0
##       mhv     3
##    tpoprE     0
##  nhwhiteE     0
##    nhblkE     0
##    nhasnE     0
##     hispE     0
## 
##  Missings in combinations of variables: 
##   Combinations Count   Percent
##  0:0:0:0:0:0:0   113 97.413793
##  0:1:0:0:0:0:0     3  2.586207
```

The results show two tables and two plots.  The left-hand side plot shows the proportion of cases that are missing values for each variable in the data set.  The right-hand side plot shows which combinations of variables are missing.  The first table shows the number of cases that are missing values for each variable in the data set. The second table shows the percent of cases missing values based on combinations of variables.  The results show that 3 or 2.6% of census tracts are missing values on the variable *mhv*. 

In any statistical analysis, you will need to deal with missing values. For example, if you wanted to find out the average median housing value in Oakland tracts, you would type in


``` r
mean(pm.file$mhv)
```

```
## [1] NA
```

The mean is *NA*, which tells you that there are missing values in the variable *mhv* that you need to deal with before R calculates a value. There are many ways that one can deal with missing data. One method is to just simply ignore or discard cases with a missing value.  To do this in the `mean()` function (and in many other R functions), you include the argument `na.rm = TRUE`


``` r
mean(pm.file$mhv, na.rm = TRUE)
```

```
## [1] 916729.2
```

As long as a large proportion of your data set is not missing data, simply ignoring missing data is often acceptable.  Just make sure you are transparent about what you did. 

***

<a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/">Creative Commons Attribution-NonCommercial 4.0 International License</a>.


Website created and maintained by [Noli Brazil](https://nbrazil.faculty.ucdavis.edu/)
