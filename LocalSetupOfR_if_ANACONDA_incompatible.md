---
title: "LocalSetupOfR_if_ANACONDA_incompatible.md"
output: html_document
date: "2024-11-03"
---

# Tools to show the current status
1. R: sessionInfo()
2. Miniconda: reticulate::py_list_packages(envname='textrpp_condaenv')

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

4. Uninstall R;
5. Delete miniconda folder in reticulate::miniconda_path() (/Users/macID/Library/r-miniconda-arm64) in the Library (Mac only, in the terminal, with admin privacy)
```
unlink(reticulate::miniconda_path(), recursive = TRUE, force = TRUE) # in R with reticulate installed
```
6. Delete the R folder "/Library/Frameworks/R.framework" (in Mac).
7. R 4.3.X usually works;
8. Alternative to all above due to the python virtual env issues, just run R code: reticulate::conda_remove("textrpp_condaenv")

# Further to setup the text package in R (in Mac M1, M2, ...)
[python version list](https://github.com/moomoofarm1/textPlot/blob/master/R/0_0_text_install.R)
1. Run R code
```
install.packages(c("devtools", "reticulate","text"))
# Have homebrew installed before doing eveything below
# Install the latest Java jdk + jre
system('brew install openjdk')
# Register Java locations
system('sudo echo 'export JAVA_HOME="$(/opt/homebrew/opt/openjdk@11/libexec/openjdk.jdk/Contents/Home)"' >> ~/.zshrc')
system('sudo echo 'export PATH="$JAVA_HOME/bin:$PATH"' >> ~/.zshrc')
system('sudo source ~/.zshrc') # Or using 'nano ~/.zshrc' to add the text after the command 'export' from the above two lines at the botto of the file .zshrc.
# Verify the lastest Java installation
system('/opt/homebrew/opt/openjdk/bin/java -version') # It prints 'Java Version'.
# Register Java globally
system('sudo ln -sfn /opt/homebrew/opt/openjdk/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk.jdk')
# Final verification
cat(system("echo $JAVA_HOME", intern = TRUE), "\n")
cat(system("which java", intern = TRUE), "\n")
cat(system("java -version", intern = TRUE), "\n")
```
<!--2. Run R code: reticulate::install_miniconda(force=TRUE)
3. Run R code: reticulate::conda_create(envname="textrpp_condaenv", python_version="3.9") -->
4. Run R code:
```
# reticulate::use_condaenv(condaenv = "textrpp_condaenv") # NOTRUN only if necessary.
text::textrpp_install() # This is to ensure the environment for text package to be installed.
Sys.setenv(OMP_NUM_THREADS = "1") 
Sys.setenv(KMP_DUPLICATE_LIB_OK = "TRUE") 
text::textrpp_initialize(save_profile = TRUE)
remove.packages("text")
devtools::install_github("oscarkjell/text") # Will not install the python packages.
```
<!-- 5. Run R code: reticulate::conda_install(envname="textrpp_condaenv", packages=c(
   "torch==2.2.0"
   ), pip=TRUE)    
6. (DO NOT RUN) Run R code:
   rpp_version <- c(
  "flair==0.13.0",
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
5. Run R code: rpp_version <- c("nltk==3.6.7")
6. Run R code: reticulate::conda_install(envname="textrpp_condaenv", packages=rpp_version)
7. Run R code: reticulate::use_condaenv(condaenv = "textrpp_condaenv")
8. Run R code: devtools::install_github("oscarkjell/text") # need to install conda first, the command 'install.packages("text")' do not need to install conda first.
9. Backup code (DONOTRUN): text::textrpp_install();
10. Run R code: text::textrpp_initialize(save_profile = TRUE)

NOTE: Steps 7-9 might only work for R version [4.3](https://mac.r-project.org/big-sur-arm64/R-4.3-branch/R-4.3-branch-arm64.pkg) at https://mac.r-project.org/ 

One step of all the above in the terminal: 
```
Rscript -e 'install.packages(c("devtools", "reticulate"));reticulate::install_miniconda(force=TRUE);reticulate::conda_create(envname="textrpp_condaenv", python_version="3.9");reticulate::conda_install(envname="textrpp_condaenv", packages=c("torch==2.2.0", "flair==0.13.0"), pip=TRUE);rpp_version <- c("nltk==3.6.7");reticulate::conda_install(envname="textrpp_condaenv", packages=rpp_version);reticulate::use_condaenv(condaenv = "textrpp_condaenv");devtools::install_github("oscarkjell/text");text::textrpp_initialize(save_profile = TRUE);'
```
 -->

# Further to remove some packages, like tokeinzers, if there are version clashes.
1. Run R code: library(reticulate);envname <- "textrpp_condaenv";use_condaenv(envname);py_run_string("import pip");py_run_string("pip.main(['uninstall', 'tokenizers', '-y'])")
(May work or not. DONT TRY) Or run R code in the terminal: Rscript -e 'library(reticulate);envname <- "textrpp_condaenv";use_condaenv(envname);py_run_string("import pip");py_run_string("pip.main(['uninstall', 'tokenizers', '-y'])")'
2. Run R code: reticulate::conda_install(envname="textrpp_condaenv", packages=c("tokenizers==0.14.1"), pip=TRUE)  # Due to that transformer 4.36.0 python packages require tokenizers version > 0.14.0. Might change in the future.

# OpenMP issue
Run in shell. (still testing)
> OMP: Error #15: Initializing libomp.dylib, but found libomp.dylib already initialized.
>
> OMP: Hint This means that multiple copies of the OpenMP runtime have been linked into the program

```
# Temporary solution
Sys.setenv(OMP_NUM_THREADS = "1") 
Sys.setenv(KMP_DUPLICATE_LIB_OK = "TRUE") 
```

```
find /usr /Library /opt -name libomp.dylib 2>/dev/null # find openMP runtime
# which R # find R executable
otool -L $(which R) | grep libomp
# find through all executable and ignore missing files 
for executable in /usr/local/bin/*; do
  otool -L "$executable" 2>/dev/null | grep libomp && echo "Found in: $executable"
done
```
Or
```
# Run in R console
system("for executable in /usr/local/bin/*; do otool -L \"$executable\" 2>/dev/null | grep libomp && echo \"Found in: $executable\"; done", intern = TRUE)
```
Or 
```
# dependencies
rpp_version <- c(
        "torch",
        "transformers",
        "huggingface_hub",
        "numpy",
        "pandas",
        "nltk",
        "sklearn", 
        "datasets",
        "evaluate",
        "accelerate",
        "bertopic",
        "jsonschema",
        "sentence_transformers",
        "flair",
        "umap",
        "hdbscan",
        "scipy"
      )
for (pkg in rpp_version) {
  lib_path <- system(paste("python -c \"import", pkg, "; print(", pkg, ".__file__)\""), intern = TRUE)
  if (length(lib_path) > 0 && !grepl("Error", lib_path)) {
    system(paste("otool -L", lib_path, "| grep libomp && echo 'Found OpenMP in'", pkg), intern = TRUE)
  }
}
```

```
# Virtual env dependencies
installed_packages <- reticulate::py$packages
rpp_version <- installed_packages[!(installed_packages %in% c("python", "cython"))]
for (pkg in rpp_version) {
  # Find the library path of the Python package
  lib_path <- tryCatch(
    {
      system(paste("python -c \"import", pkg, "; print(", pkg, ".__file__)\""), intern = TRUE)
    },
    error = function(e) {
      return(NULL)
    }
  )
  
  # Check if the library path is valid
  if (!is.null(lib_path) && length(lib_path) > 0 && !grepl("Error", lib_path)) {
    # Use 'otool' to check for OpenMP dependency
    result <- system(paste("otool -L", lib_path, "2>/dev/null | grep libomp && echo 'Found OpenMP in'", pkg), intern = TRUE)
    
    # Print the result if any found
    if (length(result) > 0) {
      print(result)
    }
  }
}
```

```
py_code <- "import pkg_resources
import importlib.util

# Dictionary to hold package names and their corresponding import names
packages = {}

for pkg in pkg_resources.working_set:
    pkg_name = str(pkg).split(' ')[0]
    try:
        # Check if the package can be imported with its own name
        if importlib.util.find_spec(pkg_name) is not None:
            packages[pkg_name] = pkg_name
    except ImportError:
        pass

    # Handle common discrepancies manually for well-known packages
    alternative_names = {
        'Pillow': 'PIL',
        'scikit-learn': 'sklearn',
        'beautifulsoup4': 'bs4'
    }

    if pkg_name in alternative_names:
        import_name = alternative_names[pkg_name]
        if importlib.util.find_spec(import_name) is not None:
            packages[pkg_name] = import_name

    # If package name in lowercase works as import name, use that
    elif importlib.util.find_spec(pkg_name.lower()) is not None:
        packages[pkg_name] = pkg_name.lower()"

reticulate::py_run_string(py_code)

installed_packages <- reticulate::py$packages
rpp_version <- installed_packages[!(installed_packages %in% c("python", "cython"))]
for (pkg in rpp_version) {
  # Find the library path of the Python package
  lib_path <- tryCatch(
    {
      system(paste("python -c \"import", pkg, "; print(", pkg, ".__file__)\""), intern = TRUE)
    },
    error = function(e) {
      return(NULL)
    }
  )
  
  # Check if the library path is valid
  if (!is.null(lib_path) && length(lib_path) > 0 && !grepl("Error", lib_path)) {
    # Use 'otool' to check for OpenMP dependency
    result <- system(paste("otool -L", lib_path, "2>/dev/null | grep libomp && echo 'Found OpenMP in'", pkg), intern = TRUE)
    
    # Print the result if any found
    if (length(result) > 0) {
      print(result)
    }
  }
}

```
