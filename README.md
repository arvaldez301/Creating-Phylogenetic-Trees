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
The default is to plot a phylogram, where the x-axis shows the genetic change / evolutionary distance. If you want to disable scaling and produce a cladogram instead, set the ```branch.length="none"``` option inside the ```ggtree()``` call. See ```?ggtree``` for more. The ```...``` option in the help for ```?ggtree``` represents additional options that are further passed to ```ggplot()```. You can use this to change aesthetics of the plot. Let’s draw a cladogram (no branch scaling) using thick blue dotted lines (note that I’m not mapping these aesthetics to features of the data with ```aes()``` – we’ll get to that later).
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
## Tree Annotation
The ```geom_tiplab()``` function adds some very basic annotation. The following sections will cover more advanced annotation.
### Internal Node Number
Some of the functions in ggtree for annotating clades need a parameter specifying the internal node number. To get the internal node number, use ```geom_text``` to display it, where the label is an aesthetic mapping to the "node variable" stored inside the tree object. Using the ```hjust``` option helps so that the labels are not sitting right on top of the nodes.
```
ggtree(tree)+
    geom_text(aes(label = node), hjust = -.3)
```
### Labeling Clades
To label clades, use ```geom_cladelabel()```. This will add another geom layer to annotate a selected clade with a bar indicating the clade with a corresponding label. You select the clades using the internal node number for the node that connects all the taxa in that clade.

Let's annotate the clade with the most recent common ancestor between taxa C and E (internal node 17). Let's make the annotation red. See ```?geom_cladelabel`` help for more.
```
ggtree(tree) + 
  geom_cladelabel(node=17, label="Some random clade", color="red")
```
Add another label for the clade connecting taxa G and H (internal node 21)
```
ggtree(tree) + 
  geom_tiplab() + 
  geom_cladelabel(node=17, label="Some random clade", 
                  color="red2", offset=.8) + 
  geom_cladelabel(node=21, label="A different clade", 
                  color="blue", offset=.8)
```
If you notice on the plots that were just generated, there were some problems. First, the labels would look a lot better if they were aligned. To fix this pass ```align=TRUE``` to ```geom_cladelabel()```. Now the labels are falling off the edge of the plot. This is because ```geom_cladelabel()``` is just adding this layer onto the end of the existing canvas that was originally layed out in the ggtree call. This default layout tried to optimize by plotting the entire tree over the entire region of the plot. Here is how to fix this:
```
ggtree(tree)+
    geom_tiplab()+
    geom_cladelabel(node=17, label="Some random clade", 
                  color="red2", offset=.8) + 
    geom_cladelabel(node=21, label="A different clade", 
                  color="blue", offset=.8)+
    theme_tree2()+
    xlim(0,70)+
    theme_tree()
```
Using ```theme_tree2()``` will add a scale to the x-axis showing the genetic distance. We need to set the limits of the x-axis and the following link will show you how to add in ```xlim()```(<https://stackoverflow.com/questions/3606697/how-to-set-limits-for-axes-in-ggplot2-r-plots). Adding ```theme_tree()``` at the end will override the original theme that was set at the beginning and remove the scale.

Instead of labeling you could highlight the entire clade with ```geom_highlight()```
```
ggtree(tree) + 
  geom_tiplab() + 
  geom_hilight(node=17, fill="gold") + 
  geom_hilight(node=21, fill="purple")
```
### Connecting Taxa
Some evolutionary events (e.g. reassortment, horizontal gene transfer) can be visualized with some simple annotations on a tree. The ```geom_taxalink()``` layer draws straight or curved lines between any of two nodes in the tree, allow it to show evolutionary events by connecting taxa.
```
ggtree(tree) + 
  geom_tiplab() + 
  geom_taxalink("E", "H", color="blue3") +
  geom_taxalink("C", "G", color="orange2", curvature=-.9)
```
### Practice
Let's combine everything that we just covered into one nice plot
```
ggtree(tree)+
    geom_tiplab()+
    geom_highlight(node=19, fill="pink")+
    geom_highlight(node=23, fill="steelblue")+
    geom_taxalink("C", "E", color="grey", linetype=2) +
    geom_taxalink("G", "J", color="grey", linetype=2, curvature=-.9)+
    theme_tree2()
```
You can go back to ```ggtree(...)``` to change the layout of this plot
## Advanced Tree Annotation and More Trees!!!
The following are examples of advanced tree annotations and a variety of other trees that you can make. Following each each, brief explanations of specific commands will be provided. Each example uses different datasets that you can find linked in this repository
```
#read/import the data
beast <- read.beast("<<<Where you have the flu_tree_beast.tree file stored>>>"

# supply a most recent sampling date so you get the dates
# and add a scale bar
ggtree(beast, mrsd="2013-01-01") + 
  theme_tree2() 

# Finally, add tip labels and adjust axis
ggtree(beast, mrsd="2013-01-01") + 
  theme_tree2() + 
  geom_tiplab(align=TRUE, linesize=.5) + 
  xlim(1990, 2020)
```
### Many Trees
ggtree will let you plot many trees at once, and you can facet them the normal ggplot2 way. Let’s generate 3 replicates each of 4 random trees with 10, 25, 50, and 100 tips, plotting them all.
```
set.seed(42)
trees <- lapply(rep(c(10, 25, 50, 100), 3), rtree)
class(trees) <- "multiPhylo"
ggtree(trees) + facet_wrap(~.id, scale="free", ncol=4) + ggtitle("Many trees. Such phylogenetics. Wow.")
```
### Plot tree with other data
For showing a phylogenetic tree alongside other panels with your own data, the facet_plot() function accepts a input data.frame and a geom function to draw the input data.
```
# Generate a random tree with 30 tips
tree <- rtree(30)

# Make the original plot
p <- ggtree(tree)

# generate some random values for each tip label in the data
d1 <- data.frame(id=tree$tip.label, val=rnorm(30, sd=3))

# Make a second plot with the original, naming the new plot "dot", 
# using the data you just created, with a point geom.
p2 <- facet_plot(p, panel="dot", data=d1, geom=geom_point, aes(x=val), color='red3')

# Make some more data with another random value.
d2 <- data.frame(id=tree$tip.label, value = abs(rnorm(30, mean=100, sd=50)))

# Now add to that second plot, this time using the new d2 data above, 
# This time showing a bar segment, size 3, colored blue.
p3 <- facet_plot(p2, panel='bar', data=d2, geom=geom_segment, 
           aes(x=0, xend=value, y=y, yend=y), size=3, color='blue4') 

# Show all three plots with a scale
p3 + theme_tree2()
```
### Overlay Organism Silouhettes
phylopic.org hosts free silhouette images of animals, plants, and other life forms, all under Creative Commons or Public Domain. You can use ggtree to overlay a phylopic image on your plot at a node of your choosing. Let’s show some gram-negative bacteria over the whole plot, and put a Homo sapiens and a dog on those clades we’re working with.
```
# Install and Load libraries
library(ggtree)
library(ggimage)

# Read tree data
tree <- read.tree("<<<file path>>>")

# Create a data frame with information about the organisms and their corresponding images
organism_data <- data.frame(
  organism = c("Organism1", "Organism2", "Organism3"),
  image_path = c("path/to/image1.png", "path/to/image2.png", "path/to/image3.png"),
  stringsAsFactors = FALSE
)

# Associate the image data with the tip labels in the tree
tip_data <- data.frame(label = tree$tip.label, stringsAsFactors = FALSE)
tip_data <- merge(tip_data, organism_data, by.x = "label", by.y = "organism", all.x = TRUE)

# Create the ggtree object and add the images
ggtree(tree)+
  geom_tiplab(data = tip_data, aes(image = image_path, label = label), geom = "image", size = 0.5)+
# Adjust the plot appearance
  theme_tree2() + theme(legend.position = "none")
```
