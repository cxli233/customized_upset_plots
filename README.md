# Customized Upset Plots
This repository contains scripts to produce customized upset plots, an alternative to Venn Diagrams. 

 Author: Chenxin Li, postdoctoral associate at Center for Applied Genetic Technologies, University of Georgia. 

 Contact: [Chenxin.Li@uga.edu](Chenxin.Li@uga.edu)

The `Scripts/` directory contains `.Rmd` files that generate the graphics shown below. 
It requires R, RStudio, and the rmarkdown package. 

* R: [R Download](https://cran.r-project.org/bin/)
* RStudio: [RStudio Download](https://www.rstudio.com/products/rstudio/download/)
* rmarkdown can be installed using the intall packages interface in RStudioThe code is in .Rmd format.

# Use Bar lengths to present set or subset sizes
In Venn Diagrams, we use the area to represent set or subset sizes. 
However, I have found it much easier to discern different lengths than different area. 

![Example with 4 sets](https://github.com/cxli233/customized_upset_plots/blob/master/Results/upset_full.svg)

The customized upset plot has 4 parts: 
* The upper left shows the total set sizes.
* The upper right is legend/color scheme.
* The lower left is a matrix showing subsets. E.g., when Set 1 and Set 2 are colored, it means the *intersection of Set 1 & Set 2, but not in any other sets.*
* The lower right shows the sizes of subsets.   

# Subsetting which intersect to show
With upset plot, you are subset which intersect to show. 
E.g., if I only want to show intersects involving Set 3, I can do that. 

![Example only involving Set 3](https://github.com/cxli233/customized_upset_plots/blob/master/Results/upset_3only.svg)

# Extending the upset plot to visualize other variables 
The lower right corner of the upset plot can be extended to plot other response variables. 

![Example with extended lower right corner](https://github.com/cxli233/customized_upset_plots/blob/master/Results/upset_extended.svg) 

# Try it out with real data!
I also provided some example data. 
Data from [Li et al., 2020, Genome Research](https://www.ncbi.nlm.nih.gov/pubmed/31896557)

![Example with real data](https://github.com/cxli233/customized_upset_plots/blob/master/Results/real_data_upset_extended.svg)

# Conclusions
I hope you like it and find it pretty. 
If you use this code for a publication, I'd greatly appreciate if you can cite or acknowledge this repository. 
