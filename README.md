Bee Counts Comparison across Types of Bee Sites
================

-   Authors and contributors: Katia Aristova, Simon Guo, John Lee,
    Adrianne Leung

A data analysis (inferential) project for MDS DSCI522 (Data Science
Workflows) from Group 26

## 1. Introduction

### 1.1 Background

Bumble bee population are experiencing rapid decline in North America. In the last 20 years, certain bumble bee species abundance decreased by 96% across the United States (Cameron et al., 2011). This phenomenon is not limited to North America, European bee colonies also experience collapses and sudden bee deaths (Bacandritsos et al., 2010; Scheper et al., 2014). Researchers proposed a variety of associations and causes ranging from pesticide use (Henry et al., 2012), to mite infestation (Brettel & Martin, 2017), as all as habitat loss and climate change. The status of bee population health, diversity and abundance remains an important topic.

### 1.2 Bumble Bee Survey

As part of Ontario's Pollinator Action Plan, the Ministry of Environment, Conservation and Parks conducted Bumble Bee Diversity and Abundance Survey. The dataset for this project comes from this survey which can be found on the Open Data Ontario website:
[link](https://data.ontario.ca/dataset/bumble-bee-diversity-and-abundance-survey "Bumble Bee Diversity and Abundance Survey"). The raw `csv` dataset can be accessed via this link: [dataset file](https://files.ontario.ca/moe_mapping/downloads/4Other/PHAP/Bumble_Bee_Public_Data.csv "Bumble Bee Public Data").

Surveyors collected bumble bee counts at 67 location in southwestern Ontario, according to the dataset (please note, that the website says 46 but the dataset contains 67 locations) between 2015 and 2017. The methodology is absent from the website. Each site contains multiple observations for the same day. Sites were encoded as agricultural, natural or semi-natural remnant. 

The features include, species and bee type (e.g. borealis species worker), county, year, date, plant information (sparse, many missing values).

Fig. 1 displays locations of each site. Southern Ontario is a highly populated sprawling metropolitan area with agriculture surrounding the urban and suburban areas. Natural sites are limited to conservation areas, parks and other protected areas. There are three different types of bee sites, namely:
* Agricultural
* Natural
* Semi-natural remnant

[View map](http://www.moegisportal.ca/agol/AccessibleViewer/index.html?appid=e38703b018ff4e3ca09f2878879a4e34)

### 1.3 Project Question 
In this project, we are aiming to answer the following question: 
Do the bumble bee counts vary depending on the type of site?

**Null hypothesis**: The median bee count values are the same at agricultural, natural and semi-natural remnant sites in Southern Ontario.  
**Alternative hypothesis**: The median bee count is not the same at agricultural, natural and semi-natural remnant sites in Southern Ontario.  

Answering this question is important to see how the site type would impact the bee counts occurring in agricultural, natural and semi-natural sites. This can help direct planning in favour of pollination and bee health in agriculture, natural and semi-natural ecosystems.

Result of the analysis will be reported as a table of the hypothesis test statistics and p-value in the final report.

**Exploratory data analysis (EDA)**  
Each row of the data set represents the number of bee counts of each species at the sampled bee site at the specified sample date. We are interested in the total bee count regardless of species, time, year, or bee type (worker, queen, etc.). Data wrangling was necessary to summarise the data set by grouping the sample date by year, bee site type and site id. Each row of the simplified dataset represents the number of bee counts per site.

Prior to analysis, we performed EDA to assess the type of distribution and density of bee counts at each type of site, as well as to assess median and mean of bee counts at each site type. The distribution of bee counts of types of site are represented with histogram plot:

![fig 1](https://github.com/UBC-MDS/DSCI_522_Bee_Count_Comparison_Group_26/blob/main/src/figures/bee_df_hist.png)

and the density is displayed with violin plots combined with indication of median and mean values of bee counts on the same plot:

![fig 2](https://github.com/UBC-MDS/DSCI_522_Bee_Count_Comparison_Group_26/blob/main/src/figures/bee_df_violin.png)

The full EDA report can be found
[here](https://github.com/UBC-MDS/DSCI_522_Bee_Count_Comparison_Group_26/blob/main/src/eda_bee.md "Exploratory data analysis report").

### 1.4 Methodology
As observed in EDA, the the data is right skewed. To answer the inferential question posted above, we plan to do a hypothesis test for independence of a difference in mean bee counts by site type using Poisson regression analysis. Since the response variable, count of bees, is discreet, Poisson is a more suitable regression test. In contrast with Linear regression, the coefficients in Poisson regression are multiplicative. The coefficient estimates need to be converted by putting *e* to the power of the estimate. The resulting number is the multiplicative difference in average bee counts (i.e. site type Natural has x times more bees on average than Agricultural).

**One vs all**  
Example:
* Agricultural mean vs natural mean
* Agricultural mean vs semi-natural remnant mean
* Natural mean vs semi-natural remnant mean

**Assumptions**  
The Poisson regression analysis assumes that all observations are independent. As mentioned earlier, this assumption is likely satisfied as multiple observations were taken on the same day at each site using random sampling technique. More assumptions related to the use of regression are described in the full report.

**Limitations**  
The dataset is small. The exact collection methodology is unknown. Since the dataset was collected by the Ministry of Environment in Ontario, we assume that the data was sampled randomly and independently. The researchers controlled for seasonality by only sampling between May and August.

## 2.Report
The final report can be found [here](https://github.com/UBC-MDS/DSCI_522_Bee_Count_Comparison_Group_26/blob/main/docs/report_bee.md).

## 3. Usage
### 3.1 Using Make file

1. To replicate the report and analysis: clone the GitHub repo and install dependencies listed below. Run the following command from the root folder to create the report:

`make all`

2. To reset the repo to its original clean state, run the following:

`make clean`
### 3.2 Usage without Make file.
To replicate the analysis, clone this GitHub repository, install the
dependencies listed below, and run the following commands at the command
line/terminal from the root directory of this project in the order below.
Please note, this option creates additional files in the directory. They have to be removed manually.
The preferable option is to use Make file (as described in section 2.2 above).

```
# download data
python src/download_data.py --url=https://files.ontario.ca/moe_mapping/downloads/4Other/PHAP/Bumble_Bee_Public_Data.csv --out_file=data/raw/Bumble_Bee_Public_Data.csv

# preprocess data
Rscript src/preprocess.R --input=data/raw/Bumble_Bee_Public_data.csv --out_dir=data/processed/

# plot figures for eda report
Rscript src/plot_eda_figures.R --csv_file=data/processed/processed_Bumble_Bee_Public_Data.csv --out_dir=src/figures

# run eda report and create html file
Rscript -e "rmarkdown::render('src/eda_bee.md')"

# run analysis
Rscript src/glm_analysis.R --file_path=data/processed/processed_Bumble_Bee_Public_Data.csv --output_folder=data/processed

# run test results
Rscript src/results_bee.R --test_agri=data/processed/agri_table.csv --test_nat=data/processed/nat_table.csv --out_dir=results

# render final report
Rscript -e "rmarkdown::render('doc/report_bee.Rmd', output_format = 'html_document')"
```

## 4. Dependencies

-   Python 3.9.5 and Python packages:
    -   docopt==0.6.2
    -   requests==2.25.1
    -   pandas==1.3.2
-   R version 4.1.1 and R packages:
    -   knitr==1.33
    -   docopt==0.7.1
    -   tidyverse==1.3.1
    -   ggplot2==3.3.5
    -   boot==1.3.28
    -   testthat==3.1.0
    -   broom==0.7.9

Dependency diagram (created by [makefile2graph](https://github.com/lindenb/makefile2graph)):
![Dependency diagram](https://github.com/UBC-MDS/DSCI_522_Bee_Count_Comparison_Group_26/blob/main/dependency_diagram.png)


## 5. License

The materials used in this project are licensed under the Open
Government Licence - Ontario. If re-using/re-mixing please provide
attribution and link to this [webpage](https://www.ontario.ca/page/open-government-licence-ontario).

## 6. References

Bacandritsos, N., Granato, A., Budge, G., Papanastasiou, I., Roinioti, E., Caldon, M., ... & Mutinelli, F. (2010). Sudden deaths and colony population decline in Greek honey bee colonies. _Journal of invertebrate pathology, 105_(3), 335-340.

Brettell, L. E., & Martin, S. J. (2017). Oldest Varroa tolerant honey bee population provides insight into the origins of the global decline of honey bees. _Scientific reports, 7_(1), 1-7.

Cameron, S. A., Lozier, J. D., Strange, J. P., Koch, J. B., Cordes, N., Solter, L. F., & Griswold, T. L.   (2011). Patterns of widespread decline in North American bumble bees. _Proceedings of the National Academy of Sciences, 108_(2), 662-667.

Henry, M., Beguin, M., Requier, F., Rollin, O., Odoux, J. F., Aupinel, P., ... & Decourtye, A. (2012). A common pesticide decreases foraging success and survival in honey bees. _Science, 336(6079)_, 348-350.

R Core Team. 2021. R: A Language and Environment for Statistical Computing. Vienna, Austria: R Foundation for Statistical Computing. https://www.R-project.org/.

Roback, Paul, and Julie Legler. 2021. Beyond Multiple Linear Regression: Applied Generalized Linear Models and Multilevel Models in r. CRC Press.

Scheper, J., Reemer, M., van Kats, R., Ozinga, W. A., van der Linden, G. T., Schaminée, J. H., ... & Kleijn, D. (2014). Museum specimens reveal loss of pollen host plants as key factor driving wild bee decline in The Netherlands. _Proceedings of the National Academy of Sciences, 111_(49), 17552-17557.

Wickham, Hadley, Mara Averick, Jennifer Bryan, Winston Chang, Lucy D’Agostino McGowan, Romain François, Garrett Grolemund, et al. 2019. “Welcome to the tidyverse.” Journal of Open Source Software 4 (43): 1686. https://doi.org/10.21105/joss.01686.

Xie, Yihui. 2014. “Knitr: A Comprehensive Tool for Reproducible Research in R.” In Implementing Reproducible Computational Research, edited by Victoria Stodden, Friedrich Leisch, and Roger D. Peng. Chapman; Hall/CRC. http://www.crcpress.com/product/isbn/9781466561595.
