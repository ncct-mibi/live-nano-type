# live-nano-type
Live bacterial species typing with Oxford Nanopore sequencing reads

## Description
This is a collection of tools and scripts which are used to perform real-time bacterial species typing of Nanopore reads as they come off the instrument.
It is built around the [Japsa framework](https://github.com/mdcao/japsa), and consists of the following: 

1. Set up an analysis server
2. Stream data to the analysis server
3. Read output from server and visualize on a web page

## Prototype 
This is just a prototype, used to generate files needed for the dev of the web page.

First, start `netcat` to listen to a specific port:
```
nc -k -l 127.0.0.1 3456 > typing-test.tsv
```

On a machine with a lot of RAM, this pipeline streams the output of `jsa.np.rtSpeciesTyping` to the specified port:
```
minimap2 -a -x map-ont -t 6 -K 100000 --secondary=no \
~/db/SpeciesTyping/Bacteria/genomeDB.mmi data/*.fastq | \
jsa.np.rtSpeciesTyping -bam - -index ~/db/SpeciesTyping/Bacteria/speciesIndex --read 50 -time 10 -out - | \
jsa.util.streamClient --input - --server 127.0.0.1:3456
```

The `minimap2` database used is constructed from the RefSeq bacterial genomes from NCBI, and is already indexed (`minimap2 -x map-ont -d genomeDB.mmi`) to speed up analysis. Still, it is quite big and a machine with at least 24 Gb RAM is needed.   
