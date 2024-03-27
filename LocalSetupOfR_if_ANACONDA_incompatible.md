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

# Further to setup the text package in R
[python version list](https://github.com/moomoofarm1/textPlot/blob/master/R/0_0_text_install.R)
1. Run R code: install.packages(c("devtools", "reticulate"))
2. Run R code: reticulate::install_miniconda(force=TRUE)
3. Run R code: reticulate::conda_create(envname="textrpp_condaenv", python_version="3.9") 
4. Run R code: reticulate::conda_install(envname="textrpp_condaenv", packages=c("torch==2.0.0", "flair==0.13.0"), pip=TRUE)
5. Run R code:
   rpp_version <- c(
  "transformers==4.36.0",
  "huggingface_hub==0.20.0",
  "numpy==1.26.0",
  "pandas==2.0.3",
  "nltk==3.6.7",
  "scikit-learn==1.3.0",
  "datasets==2.16.1",
  "evaluate==0.4.0",
  "accelerate==0.26.0",
  "bertopic==0.16.0",
  "jsonschema==4.19.2",
  "sentence-transformers==2.2.2",
  "umap-learn==0.5.4",
  "hdbscan==0.8.33"
  )
7. Run R code: reticulate::conda_install(envname="textrpp_condaenv", packages=rpp_version)
8. Run R code: devtools::install_github("oscarkjell/text")

# Further to remove some packages, like tokeinzers, if there are version clashes.
1. Run R code: library(reticulate);envname <- "textrpp_condaenv";use_condaenv(envname);py_run_string("import pip");py_run_string("pip.main(['uninstall', 'tokenizers', '-y'])")
(May work or not. DONT TRY) Or run R code in the terminal: Rscript -e 'library(reticulate);envname <- "textrpp_condaenv";use_condaenv(envname);py_run_string("import pip");py_run_string("pip.main(['uninstall', 'tokenizers', '-y'])")'
3. Run R code: reticulate::conda_install(envname="textrpp_condaenv", packages=c("tokenizers==0.14.1"), pip=TRUE)  # Due to that transformer 4.36.0 python packages require tokenizers version > 0.14.0. Might change in the future.
