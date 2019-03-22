# orthomclToVenn

This is an update of the original orthomclToVenn script written by @philippbayer in order to expand the total amount of groups that can be drawn in the Venn diagram plot. This was achieved using the pyvenn library developed by @tctianchi. It has also been rewritten in Python3.

This script takes the output from OrthoMCL, gives you the number of either ortholog clusters or genes shared between clusters, includes the number of genes that are not in clusters, and returns a Venn diagram up to 6 different groups. 

WARNING: Although this script can make Venn diagrams up to 6 groups, it will still give you the number of shared genes and shared groups for 1 to infinity families, so you can plug those numbers into whatever software you use to draw Venn diagrams in case of requiring a greater number of groups.

Weighted Venn diagram:

![Weighted Venn](figure_weighted.png)

Unweighted Venn diagram:

![Unweighted Venn](figure_unweighted.png)

# Requirements

- python 3
- python packages: `matplotlib` (`pip install matplotlib`)

# How to run it

## Input files

- groups.txt - this is the output from orthoMCL
- singletons.txt - this file is produced by running orthoMCL's `orthomclSingletons` and contains all genes that are contained in no groups.
- families.txt - this file details which species are in which groups, first the name of the group, then the short handles that appear in groups.txt. Example:

```
Milletoids: Gma,Caj,Vir,Adz,G19,BAT
Galegoids: Lja,Clo,Red,Mtr,Des,Kab
Dalbergoids: Ari,Ard
```

This file does not necessarily need to include all species contained in groups.txt.

There is a tiny example input dataset in the `example_data` folder, compare with that one if you, for example, want to use clusters that are not from OrthoMCL.

## Running it

```
python3 OrthomclToVenn.py -h
usage: OrthomclToVenn.py [-h] groups singletons families figure_name table_name

Parses orthomcl groups.txt and singletons, plots a Venn diagram of the number
of genes in shared and non-shared clusters

Positional arguments:
    groups      Path to groups.txt
    singletons  Path to singletons file (use orthomclSingletons, part of
    orthomcl)
    families    Path to file detailing groups of species/families - see README
    figure_name      Output path for final figure of shared genes (add .svg for SVG picture, add .png
                for PNG etc.)
    table_name       Output path for final table of groups

    optional arguments:
        -h, --help  show this help message and exit
```

So for example:

    python3 OrthomclToVenn.py groups.txt singletons.txt \
        dalbergiods_millettoids_galegoids.txt figure.png dalbergiods_millettoids_galegoids_table.csv

It should then print something like:

```
INFO:__main__:Started parsing.
INFO:__main__:Got 14 species in 3 families/groups.
INFO:__main__:Parsing groups.txt.
INFO:__main__:Writing cluster numbers to dalbergiods_millettoids_galegoids_table.csv
INFO:__main__:Also printing cluster numbers here:
INFO:__main__:Group	Genes overlap	Clusters overlap
INFO:__main__:Galegoids:Milletoids	33612	3496
INFO:__main__:Dalbergoids:Galegoids	2987	557
INFO:__main__:Milletoids	65945	8109
INFO:__main__:Dalbergoids:Galegoids:Milletoids	408424	14522
INFO:__main__:Galegoids	88061	10970
INFO:__main__:Dalbergoids	37947	4033
INFO:__main__:Dalbergoids:Milletoids	4385	527
INFO:__main__:Plotting unweighted Venn diagram to figure_unweighted.png and weighted Venn diagram to figure_weighted.png
```

The table it prints out is also contained in `dalbergiods_millettoids_galegoids_table.csv` in case you already have your Venn diagrams and only need the numbers. Two figures will be created: one is weighted (where the size of the circle is relational to the number of genes in it) and one is un-weighted (where the size of the circle is always the same).

# What it does

It collects the species that occur in each of the groups reported by OrthoMCL. If all genes in the group belong to only one of the user-supplied families then all genes in that cluster belong to that one family. If genes from species that appear in two families are in that cluster, then all genes in that cluster are shared between the two families, etc. pp. As the last step it will count all genes not in any OrthoMCL groups and will add them to the relevant families, these genes can't be shared.

# TODO
