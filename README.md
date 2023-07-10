# Creating-Phylogenetic-Trees
This will cover how to use the ggtree package in R studio to visualize and annotate phylogenetic trees.

## The ggtree Package
This R package extends ggplot2 for visualizing and annotating phylogenetic trees with their covariates and other associated data. It is available from Bioconductor, which you make have already previously installed. For this package, once you have installed it through Bioconductor, you won't have to do it again. To install use the following:
```
BiocManager::install("ggtree")
```
Once you have installed the package, you can then load the library. To operate the ```ggtree``` package, you must load the ```tidyverse``` package first:
```
library(tidyverse)
library(ggtree)
```
## Tree import
Most tree viewer software (including R packages) focus on Newick and Nexus file formats. ```ggtree``` supports several file formats including: Newick, Nexus, Phylip, Jplace, and New Hampshire eXtended format(NHX). The ```ggtree``` package implements several parser functions, including:
    ```read.tree``` for reading Newick files.
    ```read.phylip``` for reading Phylip files.
    ```read.jplace``` for reading Jplace files.
    ```read.nhx``` for reading NHX files.
    ```read.beast``` for parsing output of BEAST
    ```read.codeml``` for parsing output of CODEML (rst and mlc files)
    ```read.codeml_mlc``` for parsing mlc file (output of CODEML)
    ```read.hyphy``` for parsing output of HYPHY
    ```read.jplace``` for parsing jplace file including output from EPA and pplacer
    ```read.nhx``` for parsing NHX file including output from PHYLODOG and RevBayes
    ```read.paml_rst``` for parsing rst file (output of BASEML and CODEML)
    ```read.r8s``` for parsing output of r8s
    ```read.raxml``` for parsing output of RAxML
## Creating Basic Trees
For this tutorial we will be using the tree_newick.nwk that can be found within this reposatory. This is a made up phylogeny with 13 samples. Once you have loaded the libraries we can begin importing the data
```
tree <- read.tree("<<<Where you have the tree_newick.nwk file stored>>>")
tree
```
### Basic Canvas
Just like with ggplot2 we created a basic canvas with ggplot(...) and added layers with +geom_???(), we can do the same here. The ggtree package gives us a geom_tree() function. Because ggtree is built on top of ggplot2, you get ggplot2’s default gray theme with white lines. You can override this with a theme from the ggtree package.
```
# build a ggplot with a geom_tree
ggplot(tree) + geom_tree() + theme_tree()

# This is convenient shorthand
ggtree(tree)
```
### Treescale geom
To add a scale bar to your tree you will use ```geom_treescale()```. Alternatively, you can use ``threme_tree2()``` to add a scale onto the x-axis. The horizontal dimension in this example plot shows the amount of genetic change, and the branches represent evolutionary lineages changing over time. The longer the branch in the horizontal dimension, the larger the amount of change. The units of branch length are usually nucleotide substitution per site - the number of changes or substitutions divided by the length of the sequence.
```
# add a scale
ggtree(tree) + geom_treescale()

# or add the entire scale to the x axis with theme_tree2()
ggtree(tree) + theme_tree2()
```
The default is to plot a phylogram, where the x-axis shows the genetic change / evolutionary distance. If you want to disable scaling and produce a cladogram instead, set the ```branch.length="none"``` option inside the ```ggtree()``` call. See ```?ggtree``` for more. The ```...``` option in the help for ?ggtree represents additional options that are further passed to ```ggplot()```. You can use this to change aesthetics of the plot. Let’s draw a cladogram (no branch scaling) using thick blue dotted lines (note that I’m not mapping these aesthetics to features of the data with ```aes()``` – we’ll get to that later).
```
#Disable scaling
ggtree(tree, branch.length="none")

#addiing aesthetics
ggtree(tree, branch.length="none", color="blue", size=2, linetype=3)

#Creating a slanted phylogenetic tree
ggtree(tree, layout = 'slanted')

#Creating a circular phylogenetic tree
ggtree(tree, layout = "circular")

#Creating a circular unscaled cladogram with thick red lines
ggtree(tree, layout = "circular", branch.length = "none", color = "red", size = 3)
```
### Other tree geoms
In this example, you are going to add additional layers to the basic tree canvas. This will add node and tip points and label them.
```
# create the basic plot
ggtree(tree)+
  # add node points
  geom_nodepoint()+
  # add tip points
  geom_tippoint()+
  # Label the tips
  geom_tiplab()
```
### Practice
Similar to how you would change the aesthetics of the tree inside the ```ggtree()``` command, you can also change the aesthetics of the points themselves by passign graphical parameters inside the ```geom_nodepoint()``` or ```geom_tippoint()```
```
ggtree(tree)+
  geom_tiplab(color = "purple")+
  geom_tippoint(shape = 18)+
  geom_nodepoint(color = "yellow", alpha = 1)
```
## Tree Anotation
### Internal Node Number
### Labeling Clades
### Connecting Taxa
### Practice
## Advanced Tree Annotation
