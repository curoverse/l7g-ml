**Basic Principles of Tiling**

Tiling abstracts a called genome by partitioning it into overlapping variable length shorter sequences, known as tiles. A tile is a genomic sequence that is braced on either side by 24 base (24-mer) &quot;tags&quot;.

A tile sequence must be at least 248 base pairs long where each tile is labeled with a &quot;position&quot; according to the number of tiles before it. One tile position can have multiple tile variants, one for each sequence observed at that position. When a variation occurs on a tag, we allow tile variants to span multiple steps where the tags would normally end. These tiles that span multiple steps are known as &quot;spanning tiles&quot;.

Our choice of tags (&quot;tag-set&quot;) partition the human reference genome into 10,655,006 tiles, composed of 3.1 billion bases (with an average of 314.5 bases per tile). The set of all positions and tile variants are stored in is what we call the tile library. An individual&#39;s genome can then be easily represented as an array of tag sets that referencetiles in the tile library. Each position in the array corresponds to a tile position and points to the tile variant observed at that position for that individual.

To create the tiled genomes, we use Lightning, a system that allows for efficient access to large scale population genomic data with a focus on clinical and research use. The Lightning system is a combination of a conceptual way to think about genomes (genomic tiling), the internal representation of genomes for efficient access, and the software that manages access to the data.

**Pythonic Tiling Representation**

For easier use in machine learning, we converted the tiled genomes into integer numpy arrays: one row per individual genome in the population and two columns per tile position (one for each phase). Each integer represents a particular tile variant for that tile position. Phase A and Phase B tiles are interleaved.

Tile variants that span multiple tile positions are denoted by an integer and by subsequent -1&#39;s to indicate the existence of a spanning tile over those tile positions. Low quality tiles are represented by -2.

**Data Files**

The tiled genomes and their associated data are available in the following files:

_ **names** _

* This npy file contains the names of the cgf (compact genome format) files that were converted to give us the numpy arrays. cgf files are simply files containing the tiled data stored in an efficient, compact format. The cgf files should have the same basename as the original genome files that were tiled (gff, gvcf, etc), just a different extension (cgf).

* If the numpy arrays were generated on Arvados, the path may be relative to the Keep content addressable storage system. If the data needed to be cleaned before it was tiled, you will see the prefix &quot;cleaned&quot; in front of the basename.

_ **all** _

* This npy file contains a matrix containing all tile variants represented as integers (a row represents a called genome, each column represents a tile position for allcalled genomes). Low quality tiles are represented by -2 and spanning tiles are represented by -1. Low quality tiles are tile variants where at least 1 no-call was present in that variant.

_ **all-info** _

* This npy file contains the &quot;names&quot; of each tile position in the full set (all) in terms of band, step, and phase. It is represented as a single number hexadecimal format. It can be converted back to the band, step and phase for annotation purposes

We have a utility, getTileVariants.py, that takes a given tile position as input and returns information about the tile (sequence, location, and differences from the most common tile (tile variant 0) in that tile position). Note: This utility is the process of being updated.

**About the Format of The Tile Library**

**SGLF Files**

* The mapping between a reference genome and tile positions are kept in a compressed and indexed text file

* The tiling library consists of a series of sglf files The SGLF files are compressed CSV files, one per tile path. The columns are the tile id followed by a &#39;+&#39; and span information, followed by the md5sum of the tile sequence, followed by the sequence itself.

* As an example:

| 01ef.00.0000.000+1,57f5909d01b7f8edd3f8652e7e610709,ggtgaatgttggctgtggagaatgaatccgaatcacttaggtcaaaagatgactaattccaaacacttttgctctatgcctgtttttatggtggccactctttgctctcaaacagggctcagaagaagagtgccaacaagtttctccacagaggggcactggctggcatccctgtaatacgcggtttgtagagaatgaaagcagctttggttttcttttgtacga |
| --- |

**Using HGVS Annotation Tool**

The tile variant lookup tool can be found here on github:

https://github.com/arvados/l7g/tree/master/tools/get_hgvs [https://github.com/arvados/l7g/tree/master/tools/get_hgvs]

Calling the HGVS Tile Annotation Tool:

* Build docker image from docker file
* Run docker interactively with all the arguments mounted
* Run tilesearcher script inside docker

Example run:
$ ./tilesearcher.py 0001.00.0000.00b+1 /mnt/keep/by_id/ee5b90cf2d5f3573e6d455ab56e15cdf+761/hg38.fa.gz /mnt/keep/by_id/25600bbdd87544fd04e678c857c085f5+129096 /mnt/keep/by_id/7deca98a5827e1991bf49a96a0087318+233/assembly.00.hg38.fw.gz
NC_000001.11:g.[2368473T>C;2368520G>A;2368614_2368615del;2368616_2368617insGA;2368642C>T;2368718C>A]

Arguements in the above command:
tileid: 0001.00.0000.00b+1
ref: /mnt/keep/by_id/ee5b90cf2d5f3573e6d455ab56e15cdf+761/hg38.fa.gz
tilelib: /mnt/keep/by_id/25600bbdd87544fd04e678c857c085f5+129096
assembly: /mnt/keep/by_id/7deca98a5827e1991bf49a96a0087318+233/assembly.00.hg38.fw.gz


**References**

* Tiling paper preprint:[https://peerj.com/preprints/1426/](https://peerj.com/preprints/1426/)
