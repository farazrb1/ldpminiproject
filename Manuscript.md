# ldpminiproject
#this is the example project to demonstrate my knwoledge of how to use OSF linked with Rstudio and version control

---
title: How is Mercury Correlated with Dissolved Organic Carbon in Streams?
author:
  - name: Faraz Khan
    email: farazkhan@uregina.ca
    affiliation: University of Regina
    correspondingauthor: true
    footnote: 1
  - name: Britt Hall
    email: britt.hall@uregina.ca
    affiliation: University of Regina
    footnote: 1
 
address:
  - code: Some Institute of Technology
    address: Department, Street, City, State, Zip
  - code: Another University
    address: Department, Street, City, State, Zip
abstract: |
 Levels of dissolved organic carbon and mercury in streams.
keywords: 
  - methylmercury
  - dissolved organic carbon
journal: "Biogeochemistry"
date: "`r Sys.Date()`"
classoption: preprint, 3p, authoryear
bibliography: mybibfile.bib
linenumbers: true
numbersections: true
# Use a CSL with `citation_package = "default"`
# csl: https://www.zotero.org/styles/elsevier-harvard
output: 
  rticles::elsevier_article:
    keep_tex: true
    citation_package: natbib
header-includes:
 \usepackage{float}
---

```{r global_options, include=FALSE}
knitr::opts_chunk$set(fig.pos = 'H')
```

```{r setup, include=FALSE}
# I'm using R-4.0.5, so using date of 2021-05-17
library(groundhog)
groundhog.library(tidyverse, date = "2021-05-17")
groundhog.library(ggplot2, date = "2021-05-17")
groundhog.library(cowplot, date = "2021-05-17")
```

# Introduction

Dissolved organic carbon (DOC) is highly associated with methylmercury production in freshwater ecosystems. The use of optical instruments such as spectrometers in the study of DOC have led to a greater understanding of the chemical characteristics and origin of DOC. State of the art emission excitation matrices analysis have resulted in a greater understanding of DOC and its associations with MeHg in various freshwater systems such as streams. [@wallace1986response].

# Methods

To test these hypotheses, I will sample urban artificial wetlands and wet ponds from sites across Regina and Saskatoon, using methods derived from Strickman and Mitchell (2017). I will then use sediment samples from these sites in mercury methylation assays involving isotope dilution-gas chromatography-inductively coupled plasma mass spectrometry. Samples will be enriched with a stable Hg isotope to determine the Hg methylation rate constants, ambient Hg, and MeHg concentrations. 

# Results

Figure \ref{fig1} is generated using an R chunk.

```{r, include=FALSE}
dir.create("../data/")
# create README.md file for directory
## File > New File > Markdown File
# download turkey lakes watershed invertebrate density data
invert.density.url <- "https://ftp.maps.canada.ca/pub//nrcan_rncan/Forests_Foret/TLW/TLW_invertebrateDensity.csv"
invert.density.destfile <- "/Users/sam/LDP/PROD_REPRO/straus_PREE_Mock_article/data/NRCAN_1995-2009_TLW_density.csv"
## certificate issue
# curl stands for client URL
# -k means don't verify cert
## USE WITH CAUTION!!
download.file(invert.density.url, invert.density.destfile, method = "curl", extra = "-k")
# download associated metadata
invert.metadata.url <- "https://ftp.maps.canada.ca/pub//nrcan_rncan/Forests_Foret/TLW/TLW_invertebrate_metaEN.csv"
invert.metadata.destfile <- "/Users/sam/LDP/PROD_REPRO/straus_PREE_Mock_article/data/NRCAN_1995-2009_TLW_metadata.csv"
download.file(invert.metadata.url, invert.metadata.destfile, method = "curl", extra = "-k")
dat <- read.csv("../data/NRCAN_1995-2009_TLW_density.csv")
table(dat$catchment)
## catchments 31, 33, and 34 were the ones that experienced forest harvest, create new column
dat <- dat %>% mutate(harvest = if_else(catchment == "33" | catchment == "34L" | catchment == "34M" | 
                                   catchment == "34U", true = "yes", false = "no"))
table(dat$harvest)
# for the purposes of this mock assignment, let's just stick on with one genus: Baetis (a genus of mayfly, the order of which are stream quality indicators 
head(dat)
baetis <- dat %>% select(catchment, year, month, replicate, Baetis, harvest)
baetis.sum <- baetis %>% group_by(catchment, year, month, harvest) %>% summarize(mean.dens = mean(Baetis))
## need date format to plot as a time series
table(baetis.sum$month)
baetis.sum <- baetis.sum %>% mutate(month2 = if_else(month == "june", "06", if_else(month == "may", "05", 
                      if_else(month == "november", "11", if_else(month =="october", "10",
                      if_else(month == "september", "09", "0"))))))
# unite month and year
baetis.sum <- baetis.sum %>% unite(col = "year.month", year, month2, sep = "-")
## adding a day to get into yyyy-mm-dd format that R likes, the data file does not specify the day, so we're just going to use the first of the month
baetis.sum <- baetis.sum %>% mutate(ymd = as.Date(paste(year.month,"-01",sep="")))
## double check that are actually in a date format
str(baetis.sum$ymd) # nice!
# check out distribution
hist(baetis.sum$mean.dens)
hist(log10(baetis.sum$mean.dens))
p <- ggplot(baetis.sum, aes(x=ymd, y=mean.dens, color = harvest)) +
  geom_line() + 
  xlab("Year")+
  ylab("Mean density (# per sq. m)")+
  scale_y_log10()+
  theme_cowplot()
p
```

```{r fig1, fig.width = 5, fig.height = 3, fig.align='center', out.width="75%", fig.cap = "\\label{fig1}Baetis density in harvested and non-harvested catchments", echo = FALSE}
p
```

# Discussion

The results may be able to influence landscape planning and facilitate further insight into the relationship between methylmercury and bioavailability of inorganic mercury in urban environments. A great understanding of the interactions in our built environment and how it contributes to human caused ubiquitous ecological disruption (Policy Horizons 2018) can ensure that we, as a society, move towards more sustainable models of development. Benthic invertebrates are commonly used as indicators of stream water quality [@guilpart2012use]. *Baetis* sp. (Ephemeroptera) are used as stream quality indicators for catchments [@wallace1986response].

# References {.unnumbered}
