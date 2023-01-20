# Customized Upset Plots

Space holder for Zenodo: 

This repository contains scripts to produce customized upset plots, an alternative to Venn diagrams. 

 * Author: Chenxin Li, postdoctoral associate at Center for Applied Genetic Technologies, University of Georgia. 

 * Contact: [Chenxin.Li@uga.edu](Chenxin.Li@uga.edu) | [@ChenxinLi2](https://twitter.com/ChenxinLi2)

The `Scripts/` directory contains `.Rmd` files that generate the graphics shown below. 
It requires R, RStudio, and the rmarkdown package. 

* R: [R Download](https://cran.r-project.org/bin/)
* RStudio: [RStudio Download](https://www.rstudio.com/products/rstudio/download/)
* rmarkdown can be installed using the intall packages interface in RStudio

# Use bar lengths to present set or subset sizes
In Venn diagrams, we use the area to represent set or subset sizes. 
However, I have found it much easier to discern different lengths than different area. 

![Example with 4 sets](https://github.com/cxli233/customized_upset_plots/blob/master/Results/upset_full.svg)

This is a workflow for set/intersect visualization using [UpSet plots](https://github.com/hms-dbmi/UpSetR). 
The upstream segment of the workflow (intersect size determination) is based on the re-implementation of `UpSetR` by [ComplexHeatmap](https://jokergoo.github.io/ComplexHeatmap-reference/book/). 
List, data frame, and plot handling was provided by the [tidyverse](https://www.tidyverse.org/). 
Lastly, construction of composite plots is provided by [patchwork](https://cran.r-project.org/web/packages/patchwork/vignettes/patchwork.html).

In traditional [upset plots](https://upset.app/), intersects/subsets are indicated by dots. 
When two dots are connected by a line, it represents the distinct intersect between the two sets. 
Set and intersect sizes are then represented by bars. 

The workflow produces customized upset plot where intersects/subsets are indicated by a heatmap.
The customized upset plot has 4 parts: 

* The upper left shows the total set sizes.
* The upper right is legend/color scheme.
* The lower left is a matrix showing subsets. E.g., when Set 1 and Set 2 are colored, it means the *intersection of Set 1 & Set 2, but not in any other sets.*
* The lower right shows the sizes of subsets.   

# Subsetting which intersect to show
With upset plot, you can subset which intersect to show. 
E.g., if I only want to show intersects involving Set 3, I can do that. 

![Example only involving Set 3](https://github.com/cxli233/customized_upset_plots/blob/master/Results/upset_3only.svg)

# Extending the upset plot to visualize other variables 
In addition, upset plots can be extended. 
Mean separation plots (e.g., box plot, bar plot) and annotations (heatmaps) can be added to the sides of the upset plot using `patchwork`.

![Example with extended lower right corner](https://github.com/cxli233/customized_upset_plots/blob/master/Results/upset_extended.svg) 

# Try it out with real data!
I also provided some example data. 
Data from [Li et al., 2020, Genome Research](https://www.ncbi.nlm.nih.gov/pubmed/31896557)

![Example with real data](https://github.com/cxli233/customized_upset_plots/blob/master/Results/real_data_upset_extended.svg)

# Dependencies 
```{r}
library(tidyverse) 
library(patchwork) 
library(ComplexHeatmap)

library(RVenn) # Only required if you want Venn diagrams 
library(RColorBrewer) # This is for the colors only, not actually necessary
```

# Getting started 
Auxiliary dependencies: 

* For 2-3 sets, Venn diagrams can be made readily using the [RVenn package](https://cran.r-project.org/web/packages/RVenn/vignettes/vignette.html). The `ggVenn()` function from `RVenn` produces a ggplot object that is a Venn Diagram. 
* The official way to install `ComplexHeatmap` is via `devtools::install_github("jokergoo/ComplexHeatmap")`, which requires the `devtools` package.
* For mean separation plots, a suggested package is [ggbeeswarm](https://github.com/eclarke/ggbeeswarm), a violin plot, but with actual data points. 
* For color palettes, suggested are `viridis` and `RColorBrewer` packages. 
* If you want to save plot as .svg file, you may need the R package `svglite`. If you are using Mac, you may need to install XQuart. [Link](https://www.xquartz.org/)

# Getting started
Here are example scripts for 3 sets. 
The workflow is scalable to more sets, as intersect size calculation is automatic (provided by `ComplexHeatmap`). 
However, as the number of sets increases, the number of subsets increases geometrically, and thus filtering for subset of interest will be important. 
The easiest way to use this workflow is copy the code from this README file, or download one of the .Rmd files from the [Scripts/ folder](https://github.com/cxli233/customized_upset_plots/tree/master/Scripts). 
Then modify the code to suit your data and taste. 

# Data 
```{r}
my_list <- list(
  data1 = letters[1:10], 
  data2 = letters[3:13], 
  data3 = letters[6:18])
```
The required input is a list of vector. 

# If you want a Venn diagram
```{r}
my_object <- RVenn::Venn(my_list)

ggvenn(
  my_object, slice = 1:3, 
  thickness = 0.5,
  alpha = 0.5, 
  fill = brewer.pal(8, "Set2")
) +
  theme_void() +
  theme(
    legend.position = "none"
  )

ggsave("../Results/VennDiagram_quick_start.svg", height = 4, width = 4, bg = "white")
ggsave("../Results/VennDiagram_quick_start.png", height = 4, width = 4, bg = "white")
```
![example venn diagram](https://github.com/cxli233/customized_upset_plots/blob/master/Results/VennDiagram_quick_start.svg)
`ggVenn()` only goes up to 3 sets. For more sets, it is better to use upset plot. 

# ComplexHeatmap for heavy lifting 
```{r}
comb_mat <- make_comb_mat(my_list)
my_names <- set_name(comb_mat)
my_names
```
`make_comb_mat()` from `ComplexHeatmap` calculate intersect/subset sizes. 
`make_comb_mat()` produces a matrix object from the list of vectors. The matrix itself can be filtered for intersects/subsets of interst.  
For examples, see [this .Rmd file](https://github.com/cxli233/customized_upset_plots/blob/master/Scripts/customized_upset_plot_2023_01_19_downstream_of_complexheatmap.Rmd) at section "Subsetting the intersects".  

**The rest of code is to produces the 4 pieces that make up a customized upset plot. Since every step along the way is customizable, the result can be highly tailored towards the needs and taste of the user.**

## Total set size 
```{r}
my_set_sizes <- set_size(comb_mat) %>% 
  as.data.frame() %>% 
  rename(sizes = ".") %>% 
  mutate(Set = row.names(.)) 

p1 <- my_set_sizes %>% 
  mutate(Set = reorder(Set, sizes)) %>% 
  ggplot(aes(x = Set, y= sizes)) +
  geom_bar(stat = "identity", aes(fill = Set), alpha = 0.8, width = 0.7) +
  geom_text(aes(label = sizes), 
            size = 5, angle = 90, hjust = 0, y = 1) +
  scale_fill_manual(values = brewer.pal(4, "Set2"),  # feel free to use some other colors  
                     limits = my_names) + 
  labs(x = NULL,
       y = "Set size",
       fill = NULL) +
  theme_classic() +
  theme(legend.position = "right",
        text = element_text(size= 14),
        axis.ticks.y = element_blank(),
        axis.text = element_blank()
        ) 
```

## Legend 
It's not easy to extract legend.
But we can write a function for that.  
```{r}
get_legend <- function(p) {
  tmp <- ggplot_gtable(ggplot_build(p))
  leg <- which(sapply(tmp$grobs, function(x) x$name) == "guide-box")
  legend <- tmp$grobs[[leg]]
  legend
}

p2 <- get_legend(p1)
```

## Overlap sizes
```{r}
my_overlap_sizes <- comb_size(comb_mat) %>% 
  as.data.frame() %>% 
  rename(overlap_sizes = ".") %>% 
  mutate(category = row.names(.))

p3 <- my_overlap_sizes %>% 
  mutate(category = reorder(category, -overlap_sizes)) %>% 
  ggplot(aes(x = category, y = overlap_sizes)) +
  geom_bar(stat = "identity", fill = "grey80", color = NA, alpha = 0.8, width = 0.7) +
  geom_text(aes(label = overlap_sizes, y = 0), 
            size = 5, hjust = 0, vjust = 0.5) +
  labs(y = "Intersect sizes",
       x = NULL) +
  theme_classic() +
  theme(text = element_text(size= 14, color = "black"),
        axis.text =element_blank(),
        axis.ticks.x = element_blank(),
        axis.title.x = element_text(hjust = 0),
        ) +
  coord_flip()
```

## Overlap matrix 
```{r}
my_overlap_matrix <- str_split(string = my_overlap_sizes$category, pattern = "", simplify = T) %>% 
  as.data.frame() 

colnames(my_overlap_matrix) <- my_names

my_overlap_matrix_tidy <- my_overlap_matrix %>% 
  cbind(category = my_overlap_sizes$category) %>% 
  pivot_longer(cols = !category, names_to = "Set", values_to = "value") %>% 
  full_join(my_overlap_sizes, by = "category") %>% 
  full_join(my_set_sizes, by = "Set")

p4 <- my_overlap_matrix_tidy %>% 
  mutate(category = reorder(category, -overlap_sizes)) %>%  
  mutate(Set = reorder(Set, sizes)) %>%  
  ggplot(aes(x = Set, y = category))+
  geom_tile(aes(fill = Set, alpha = value), color = "grey30", size = 1) +
  scale_fill_manual(values = brewer.pal(4, "Set2"), # feel free to use other colors 
                    limits = my_names) +
  scale_alpha_manual(values = c(0.8, 0),  # color the grid for 1, don't color for 0. 
                     limits = c("1", "0")) +
  labs(x = "Sets",  
       y = "Overlap") +
  theme_minimal() +
  theme(legend.position = "none",
        text = element_text(color = "black", size= 14),
        panel.grid = element_blank(),
        axis.text = element_blank()
        )
```

# Put them together 
```{r}
wrap_plots(p1, p2, p4, p3, 
          nrow = 2, 
          ncol = 2,
          heights = c(1, 2), # the more rows in the lower part, the longer it should be
          widths = c(1, 0.8),
          guides = "collect") &
  theme(legend.position = "none")

ggsave("../Results/quick_start.svg", height = 3.5, width = 3, bg = "white") 
# this should be a tall & skinny plot 
# I prefer .svg, but you can also save as phd or png 
# I will open up the .svg file and mannually adjust the size until it's good
# check that nothing is cut off from the plot 
# png is for twitter posting 
ggsave("../Results/quick_start.png", height = 3.5, width = 3, bg = "white")
```
![quick start](https://github.com/cxli233/customized_upset_plots/blob/master/Results/quick_start.svg)

# Conclusions
I hope you like it and find it pretty. 
If you use this code for a publication, I'd greatly appreciate if you can cite or acknowledge this repository. 



