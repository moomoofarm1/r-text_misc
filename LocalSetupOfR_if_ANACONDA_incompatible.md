---
title: "LocalSetupOfR_if_ANACONDA_incompatible.md"
output: html_document
date: "2023-12-27"
---

# Due to the incompatibility between miniconda and Anaconda, one has to uninstall all modules to reset the system.
1. Uninstall all envs except root in Anaconda via the UI.
2. Uninstall Anaconda;
3. Run the following R code in the console to delete all R packages;

```{r posting uninstallation code, eval = FALSE}
ip <- as.data.frame(installed.packages()[,c(1,3:4)])
rownames(ip) <- NULL
ip <- ip[is.na(ip$Priority),1:2,drop=FALSE]
print(ip, row.names=FALSE)

# Remove all user installed packages, without removing any base packages for R or MRO.
# create a list of all installed packages
ip <- as.data.frame(installed.packages())
head(ip)
# if you use MRO, make sure that no packages in this library will be removed
ip <- subset(ip, !grepl("MRO", ip$LibPath))
# we don't want to remove base or recommended packages either\
ip <- ip[!(ip[,"Priority"] %in% c("base", "recommended")),]
# determine the library where the packages are installed
path.lib <- unique(ip$LibPath)
# create a vector with all the names of the packages you want to remove
pkgs.to.remove <- ip[,1]
head(pkgs.to.remove)
# remove the packages
sapply(pkgs.to.remove, remove.packages, lib = path.lib)
```

5. Uninstall R;
6. Delete miniconda folder in reticulate::miniconda_path() (/Users/macID/Library/r-miniconda-arm64) in the Library (Mac only, in the terminal, with admin privacy)
7. Delete the R folder "/Library/Frameworks/R.framework" (in Mac).
8. Install R;
9. Alternative to all above due to the python virtual env issues, just run R code: reticulate::conda_remove("textrpp_condaenv")

# Further to setup the text package in R
[python version list](https://github.com/moomoofarm1/textPlot/blob/master/R/0_0_text_install.R)
1. Run R code: install.packages(c("devtools", "reticulate"))
2. Run R code: reticulate::install_miniconda(force=TRUE)
3. Run R code: reticulate::conda_create(envname="textrpp_condaenv", python_version="3.9")
<!--4. Run R code: reticulate::use_condaenv(condaenv = "textrpp_condaenv") <-->
5. Run R code: reticulate::conda_install(envname="textrpp_condaenv", packages=c(
   "torch==2.0.0", "flair==0.13.0"
   ), pip=TRUE)    
6. Run R code: 
 rpp_version <- c(
  "nltk==3.6.7",
  "pandas",
  "datasets",
  "evaluate",
  "bertopic==0.16.0",
  "jsonschema",
  "sentence-transformers",
  "umap-learn",
  "hdbscan"
  ) # "accelerate==0.26.0",  "huggingface_hub==0.20.0", "numpy==1.26.0",
  #"scikit-learn==1.3.0", "scipy==0.10.1",  "transformers==4.36.0",
8. Run R code: reticulate::conda_install(envname="textrpp_condaenv", packages=rpp_version) # This will install tokenizers==0.19.1 might due to transformers / huggingface_hub.
9. Run R code: reticulate::py_list_packages("textrpp_condaenv") # huggingface_hub installs tokenizers 0.19.1
10. Run R code: reticulate::conda_remove("textrpp_condaenv",c('transformers','tokenizers'))
11. Run R code: reticulate::conda_install("textrpp_condaenv",c('transformers==4.36.0','tokenizers==0.15.2')); # huggingface_hub requires tokenizers >=15 and <19.
12. Run R code: devtools::install_github("oscarkjell/text")

# Further to remove some packages, like tokeinzers, if there are version clashes.
1. Run R code: library(reticulate);envname <- "textrpp_condaenv";use_condaenv(envname);py_run_string("import pip");py_run_string("pip.main(['uninstall', 'tokenizers', '-y'])")
(May work or not. DONT TRY) Or run R code in the terminal: Rscript -e 'library(reticulate);envname <- "textrpp_condaenv";use_condaenv(envname);py_run_string("import pip");py_run_string("pip.main(['uninstall', 'tokenizers', '-y'])")'
3. Run R code: reticulate::conda_install(envname="textrpp_condaenv", packages=c("tokenizers==0.14.1"), pip=TRUE)  # Due to that transformer 4.36.0 python packages require tokenizers version > 0.14.0. Might change in the future.
